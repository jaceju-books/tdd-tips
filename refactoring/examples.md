# 實例介紹

我們從一個實際在使用的例子來探討重構的技法。這個例子主要是一個我們在處理圖片用的服務，我們把它包裝成一個 library 。

## 用途

這個 library 的實際版本其實有點龐大，為了說明方便，我簡化了一些重複性較高的部份，只留下幾個重構時必要的重點。

這個簡化過的 library 包含了幾個功能：

1. 上傳圖片。

2. 刪除圖片。

3. 一次取得多張圖片。


圖片也只分成兩種類型：

* 專輯 \(album\)

* 文章 \(article\)


更詳細的解說會在後面的章節需要時補充。

## 類別

這個 library 主要由兩個類別組成： `Api 及 ApiWrapper` 。一開始只有 `Api` 這個類別，它的操作方法如下：

* `setHttpRequest($httpRequest)`

* `getHttpRequest()`

* `checkParams($params)`


* `execute()`

當操作 `Api` 這些功能時，需要一些參數；但每次要記憶這些參數不是那麼容易，所以後來又增加了 `ApiWrapper` 這個類別來簡化一些操作步驟。它的操作方法有這些：

* `getAlbumImageInfo($indexes, $params = [])`

* `getAlbumImageUrls($indexes, $params = [])`


* `getArticleImageInfo($indexes, $params = [])`

* `getArticleImageUrls($indexes, $params = [])`


對外界來說，這些方法是不可以改變的。

## 架構



