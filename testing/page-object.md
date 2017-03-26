# Page Object

一直在思考在 Web Testing 中， PageObject 該不該包含 assertion 。
Martin Fowler 在他的 Bliki 上支持不要包含進去，原因是他認為依照 SRP 原則， assertion 應該另外包到 assertion library 裡。
http://martinfowler.com/bliki/PageObject.html
但看到 Pete Hodgson 這篇 Blog ，他解釋了在 PageObject 這個情境下，應該是走 Tell Don't Ask 而非 SRP 。換句話說，你可以直接跟 PageObject 說「檢查有沒有」就好；而不是要求它吐給你它裡面的東西，你再自行檢查。
http://blog.thepete.net/…/2013/09/13/assertions-in-page-ob…/
從這個觀念再去看 Behat Page Object Extension 的實作，大致上也是走 Tell Don't Ask 的模式。
https://github.com/sensiolabs/BehatPageObjectExtension
最重要的，是我們能不能在測試中清楚地用 PageObject 去描述我們實際的需求，而不是落在操作 UI 的思維裡。

