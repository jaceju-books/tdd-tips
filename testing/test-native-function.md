# 測試 PHP 原生函式

在可測試的架構中，通常在我們需要用到 native function 時，會將它們封裝在一個類別中；然後在使用時，透過它來生成一個物件使用。

這樣做的好處有兩個：

1. 語意化
2. 物件可用 stub 或 mock 取代

用你提到的 function 來舉個例子，假設程式碼是這樣 (原諒我例子的創意不太好) ：

```
// 將廠商提供的檔案重命名
if (rename($oldFile, $newFile)) {
    // 執行清理動作
    $output = exec(’example clean ‘ . $newFile);
}
```

首先我們建立一個類別：

```
<?php
class VendorFileCleaner
{
    private $file;

    public functon __construct($oldFile)
    {
        $this->file = $oldFile;
    }

    public functon moveToStore($newFile)
    {
        $oldFile = $this->file
        $ok = rename($oldFile, $newFile);
        if ($ok) {
            $this->file = $newFile;
        }
        return $ok;
    }

    public function clean()
    {
        return exec('example clean ' . $this->file);
    }
}
```

你會看到我把 native function 包在類別方法裡，這樣一來我可以在 PHPUnit 中使用 Mockery 來模擬它：

```
$mockClear = Mockery::mock(VendorFileCleaner::class, $oldFile);

$mockClear->shouldReceive('moveToStore')
	->with($newFile)
	->andReturn(true);
```

這樣一來就可以將 native function 抽象化，隔離對它們的依賴，對我們的商業邏輯進行測試了。

當然，不見得一定要自己寫類別，如果有已經公認是被驗證過的第三方 library 可以實現這樣的抽象化，也可以拿來使用。不確定你有沒有來上過課，在課程裡我是用 date() 這個函式示範，然後用 Carbon 這個套件抽象化日期，以便測試。

以此類推，如果是跟廠商 API 介接，那就是隔離直接操作廠商 API 的程式碼，模擬它們回傳成功結果與失敗結果的狀況，以驗證我們的程式碼有正確處理這些狀況。

如果還有什麼問題的話，敲一下我，然後約個時間來一樓找我吧。

