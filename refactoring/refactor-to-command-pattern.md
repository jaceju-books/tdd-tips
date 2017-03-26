# Command Pattern

事實上使用者一開始就決定好他們要做什麼了，這是利用多型的好時機。

我們其實不需要用 `if...else` 或 `switch`


在 `Api::execute` 中，上傳圖片會呼叫 `uploadImage` 這個私有方法來做事，而刪除圖片則會呼叫：

```php
public function execute()
{
    switch ($this->params['method']) {
        case 'upload':
            return $this->uploadImage();
        case 'delete':
            return $this->deleteImage();
        default:
            return $this->getInfo();
    }
}
```

