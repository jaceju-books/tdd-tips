# 消滅重複的程式碼

## `ApiWrapper` 的壞味道

首先在 `ApiWrapper` 這個類別其實有很多重複的程式碼，我們先從 `getAlbumImageInfo` 與 `getAlbumImagePaths` 、 `getArticleImageInfo` 、 `getArticleImagePaths` 等四個方法來看：

```php
public function getAlbumImageInfo($indexes, array $params = [])
{
    $defaultParams = [
        'method' => 'list',
        'type' => 'album',
        'dimension' => '100x100',
    ];

    $newParams = array_merge(
        $defaultParams,
        $params
    );

    return $this->getImageInfo($indexes, $newParams);
}

public function getAlbumImagePaths($indexes, array $params = [])
{
    $defaultParams = [
        'method' => 'list',
        'type' => 'album',
        'dimension' => '100x100',
    ];

    $newParams = array_merge(
        $defaultParams,
        $params
    );

    return $this->getImagePaths($indexes, $newParams);
}

public function getArticleImageInfo($indexes, array $params = [])
{
    $defaultParams = [
        'method' => 'list',
        'type' => 'article',
        'dimension' => '100x100',
    ];

    $newParams = array_merge(
        $defaultParams,
        $params
    );

    return $this->getImageInfo($indexes, $newParams);
}
```

從這裡可以發現幾個重複的地方：

1. 同類型的 `$defaultParams` 其實是一樣的。
2. 建立 `$newParams` 的程式碼也是一樣的。

我們先來解決第一個問題。

## 提煉共用的設定值

當預設參數散落在各方法時，其實修改起來是不容易的。如果我們把它集中在一個設定檔裡，在管理上也比較方便些。因此我新增了一個 `config.php` ，並將 `album` 與 `article` 的設定都複製進來：

```php
<?php
return [
    'types' => [
        'album' => [
            'method' => 'list',
            'type' => 'album',
            'dimension' => '100x100',
        ],
        'article' => [
            'method' => 'list',
            'type' => 'article',
            'dimension' => '100x100',
        ],
    ],
];
```

接下來在 `ApiWrapper` 初始化時就載入這個設定檔：

```php
private $config;

public function __construct()
{
    // ...
    $this->config = require __DIR__ . '/config.php';
}
```

再把 `getAlbumImageInfo` 重構成：

```php
public function getAlbumImageInfo($indexes, array $params = [])
{
    $type = 'album';
    $defaultParams = $this->config['types'][$type];
    $newParams = array_merge(
        $defaultParams,
        $params
    );

    return $this->getImageInfo($indexes, $newParams);
}
```

其他三個方法以此類推。

## 提煉重複的程式碼

現在這四個方法中都有這段重複的程式碼：

```php
$defaultParams = $this->config['types'][$type];
$newParams = array_merge(
    $defaultParams,
    $params
);
```

利用提煉方法，我們把它獨立到 `mergeDefaultParams` 這個私有方法裡，現在程式碼變成：

```php
public function getAlbumImageInfo($indexes, array $params = [])
{
    $type = 'album';
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->getImageInfo($indexes, $newParams);
}

// ...

private function mergeDefaultParams($type, array $params): array
{
    $defaultParams = $this->config['types'][$type];
    $newParams = array_merge(
        $defaultParams,
        $params
    );
    return $newParams;
}
```

這樣程式碼感覺清楚多了，但還是有進一步簡短的空間。

## 改用魔術方法來移除重複的程式

現在這四個方法已經看起來很像了：

```php
public function getAlbumImageInfo($indexes, array $params = [])
{
    $type = 'album';
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->getImageInfo($indexes, $newParams);
}

public function getAlbumImagePaths($indexes, array $params = [])
{
    $type = 'album';
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->getImagePaths($indexes, $newParams);
}

public function getArticleImageInfo($indexes, array $params = [])
{
    $type = 'article';
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->getImageInfo($indexes, $newParams);
}

public function getArticleImagePaths($indexes, array $params = [])
{
    $type = 'article';
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->getImagePaths($indexes, $newParams);
}
```

我們可以利用 `__call` 這個魔術方法把它們全集中在一起：

```php
public function __call($name, $arguments)
{
    if (!preg_match('#^get(\w+)Image(\w+)$#', $name, $matches)) {
        return false;
    }
    if (count($arguments) === 0) {
        return false;
    }
    $indexes = $arguments[0] ?? [];
    $params = $arguments[1] ?? [];
    $type = strtolower($matches[1]);
    $format = $matches[2];
    $methodName = sprintf('getImage%s', $format);
    $newParams = $this->mergeDefaultParams($type, $params);
    return $this->$methodName($indexes, $newParams);
}
```

然後砍了那四個方法。但如果是用 IDE 開發的話，需要在類別定義裡把方法名稱加上去，好讓 IDE 可以識別：

```php
/**
 * ...
 * @method getAlbumImageInfo(array $indexes, array $params = [])
 * @method getAlbumImagePaths(array $indexes, array $params = [])
 * @method getArticleImageInfo(array $indexes, array $params = [])
 * @method getArticleImagePaths(array $indexes, array $params = [])
 */
class ApiWrapper
```

## 提煉格式轉換方法

現在我們來看 `getImagePaths` 這個方法：

```php
private function getImagePaths($indexes, array $params)
{
    $info = $this->getImageInfo($indexes, $params);
    $paths = [];
    if (is_array($info)) {
        foreach ($info as $key => $item) {
            $paths[$key] = $item['path'];
        }
    }
    return $paths;
}
```

它在取得 `getImageInfo` 方法的回傳值後，做了一次格式的轉換，只取其中的 `path` 欄位值。

但 `getImageInfo` 方法跟下方的 `if` 敘述並沒有在同一個抽象層次上 (參考《實現模式》中的「組合方法」一節) ，所以我們用提煉方法來重構：

```php
private function getImagePaths($indexes, array $params)
{
    $info = $this->getImageInfo($indexes, $params);
    return $this->pluckPath($info);
}

private function pluckPath($info): array
{
    $paths = [];
    if (is_array($info)) {
        foreach ($info as $key => $item) {
            $paths[$key] = $item['path'];
        }
    }
    return $paths;
}
```

轉換格式的部份被提煉到 `pluckPath` 方法中，現在 `getImagePaths` 已經是一個組合方法，可以很清楚表達它的意圖了。

## 實現模式






外針對同一圖片類型其實有不同的輸出格式，如果未來要擴增這些格式的話，要修改的幅度也不容小覷。


先來 `ApiWrapper::getImageInfo` 這個方法：

```php
private function getImageInfo($indexes, array $params)
{
    if (false === is_array($indexes) || (0 === count($indexes))) {
        return false;
    }
    $params['indexes'] = $indexes;

    try {
        $this->api->checkParams($params);

        return $this->api->execute();
    } catch (\Exception $e) {
        return false;
    }
}
```

這裡 `getImageInfo` 檢查參數後就會透過 `Api` 類別所派生的物件來繼續接下來的行為。







```php
private function getImageInfo($indexes, array $params)
{
    if (false === is_array($indexes) || (0 === count($indexes))) {
        return false;
    }
    $params['indexes'] = $indexes;

    try {
        $this->api->checkParams($params);

        return $this->api->execute();
    } catch (\Exception $e) {
        return false;
    }
}
```

這裡 `getImageInfo` 檢查參數後就會透過 `Api` 類別所派生的物件來繼續接下來的行為。

在檢查完參數後，會繼續呼叫 `$this->api` 的 `execute` 方法以取回指定的圖片資訊內容。

跟 `getAlbumImageInfo` 不同， `getAlbumImagePaths` 改為呼叫 `getImagePaths` 這個私有方法：

```php
private function getImagePaths($indexes, array $params)
{
    $info = $this->getImageInfo($indexes, $params);
    $paths = [];
    if (is_array($info)) {
        foreach ($info as $key => $item) {
            $paths[$key] = $item['path'];
        }
    }
    return $paths;
}
```

這裡可以看到 `getImagePaths` 是先取得 `getImageInfo` 的內容後，再一一取得每個項目裡的 `path` 值，組合出最後我們要的資訊。


