# AspectMock 實務踩雷

1. 前一個測試案例用過的 native function ，在下一個測試案例再做 Test::func mock 的話，就會失效。必須在 setUp 時就先 mock 好。


