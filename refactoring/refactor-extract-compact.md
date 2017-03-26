# 重構 extract 與 compact

## 問題

PHP 有兩個很方便的函式： [`extract`](http://php.net/manual/en/function.extract.php) 與 [`compact`](http://php.net/manual/en/function.compact.php) 。 `extract` 可以把某個關聯式陣列展開，直接變成對應的變數，例如：

```php
$params = [
    'color' => 'blue',
    'size' => 'medium',
    'shape' => 'sphere'
];
extract($params);
echo "$color, $size, $shape"; // blue, medium, sphere
```

但這在實務上不是很好的寫法，如果 `$params` 是外部參數的話，我們通常很難追蹤新變數的由來。例如：

```php
public function setParams($params)
{
    extract($params);
    echo "$color, $size, $shape"; // 這些變數有未定義的風險
}
```

而且加上它預設會覆蓋掉現有變數的值：

```php
$size = 'large';
$params = ['size' => 'small'];
extract($params);
echo $size; // small
```

這樣一來程式出現 bug 時就很難追蹤了。

另一個是行為剛好完全相反的 `compact` 函式，它主要是把數個變數轉換成關聯式陣列，例如：

```php
$color = 'blue';
$size = 'medium';
$shape = 'sphere';

$params = compact('color', 'size', 'shape');
print_r($params);

// Array
// (
//     [color] => blue
//     [size] => medium
//     [shape] => sphere
// )
```

這個函式平常不會有太大的問題，不過如果它跟 `call_user_func_array` 這個函式搭配時，例如：

```php
return call_user_func_array('compact', $params);
```

會在 PHP 7.1 造成 `Cannot call compact() dynamically` 的錯誤，原因是 PHP 7.1 不能對某些函式做動態呼叫了，詳情可以參考這個不相容特性的描述：

* [Forbid dynamic calls to scope introspection functions](http://php.net/manual/en/migration71.incompatible.php#migration71.incompatible.forbid-dynamic-calls-to-scope-introspection-functions)

## 範例

接下來我們來看一個實際的例子，它剛好踩中了上面提到的雷。

```php
class Config
{
    // ... (略) ...
	 
    public function __construct($adapter, array $params = [])
    {
        $this->adapter = $adapter;
        $this->params = $params;
    }
	 
    public function toArray()
    {
        $vars = ['driver', 'host', 'username', 'password', 'database', 'charset', 'collation', 'prefix'];
        $defaults = array_fill_keys($vars, '');
        extract($defaults); // Suppressing undefined index
        extract($this->params); // Overwrite

        $driver = $this->adapter;
        $database = $dbname;
        $collation = $this->getCollation($charset);

        return call_user_func_array('compact', $vars);
    }

    // ... (略) ...
}
```

這個類別主要是協助開發者轉換一些設定值，然後提供一個 `toArray` 方法來取得最終的設定內容。

`toArray` 方法有以下幾個問題：

1. `$dbname` 與 `$charset` 都是透過 `extract` 函式展開而來，但它們會在靜態分析時被當成沒有被初始化的變數而被警告。
2. `$driver` 、 `$database` 、 `$collation` 指定值後，卻沒有直接地被使用，而是透過 `compact` 函式來引用。這三個變數在靜態分析時，也會被當成沒有使用的變數而被警告。
3. 透過 `call_user_func_array` 函式來調用 `compact` 函式，在 PHP 7.1 中是不容許的用法。

## 重構

有幾個方法可以重構 `toArray` 這個方法，這邊主要用 `array_reduce` 這個函式來重構：

```php
    public function toArray()
    {
        $vars = ['driver', 'host', 'username', 'password', 'database', 'charset', 'collation', 'prefix'];
        $result = array_reduce($vars, function ($carry, $name) {
            $carry[$name] = isset($this->params[$name]) ? $this->params[$name] : '';
            return $carry;
        }, []);
        $result['driver'] = $this->adapter;
        $result['database'] = $this->params['dbname'];
        $result['collation'] = $this->getCollation($this->params['charset']);
        return $result;
    }
```

重構的要點如下：

1. 原本使用 `extract` 函式的部份，改為利用 `array_reduce` 來將 `$vars` 的項目逐一跟 `$this->params` 來合併。
2. 捨棄掉了 `extract` 所展開的變數， `$dbname` 與 `$charset` 的值改從 `$this->params` 取得。
3. 因為 `array_reduce` 最後得到的 `$result` 陣列就是我們需要的格式，我們只需要重新賦與其中 `'driver'` 、 `'database'` 、 `'collation'` 等項目的值即可；這麼一來就不需要再使用 `compact` 函式了。

## 練習

`Config` 類別還有一個方法：

```php
class Config
{
    // ... (略) ...

    public function getDSN()
    {
        extract($this->params);
        $array = [];
        foreach (['host', 'port', 'dbname', 'charset'] as $key) {
            if (isset($$key)) {
                $val = $$key;
                $array[$key] = "$key=$val";
            }
        }
        return 'mysql:' . implode(';', $array);
    }
    
    // ... (略) ...    
}
```

除了 `extract` 函式之外，它還有 `$$key` 的問題，這會讓程式變得不容易理解。

請試著將它重構，消除掉 `extract` 函式以及 `$$key` ，並且語法也要相容於 PHP 7.1 。



