# BDD Tool

這篇文章點出一個問題：常有開發者誤以為「用 BDD 工具」就等於在「實行 BDD 」，也常認為「 BDD 就是用在 UI / 網頁測試」，這其實都是錯誤的觀念。
BDD 就是 TDD 的延伸，它們都是一種開發的過程：當開發者一步步滿足需求的情境就可以完成系統功能，只是 BDD 用了不一樣的角度來詮譯這件事情。
BDD 注重的是系統外在的行為 (behaviour) ，而不是如何做的細節； BDD 是將系統的 specification 透過 examples 呈現，透過人話來描述它們的行為；而這些描述就是溝通的關鍵，讓需求方 (PO) 可以更容易去驗證系統的行為。

http://www.thefriendlytester.co.uk/2015/08/using-bdd-tools-to-write-automated.html

---

在公司的頻道裡討論起 BDD ，整理一下我的觀點。

BDD 著重在溝通上，而其中的 Cucumber 則是溝通的輔助工具，它的目的在於找到 PO (PM) 和開發人員之間語言的平衡點；如果只是把 Cucumber 當成另一種寫文件的格式，那導入 BDD 時通常很容易失敗。一開始由工程師寫的 Specification 很容易陷入程式細節裡，這也是為什麼 91 哥的課有一份作業是要我們練習從需求去寫出別人看得懂的測試。

另外要分享的經驗是，因為這部份必須要跟 SA (或 PM) 協調好要這麼做，所以一開始會有點卡卡的。還有就是 Scenario 的寫法也還是摸索中，像是怎麼把資料列表中哪些欄位要放進規格裡都需要討論。有滿多實戰上會遇到的問題，如果有機會的話會貼出來，歡迎大家一起討論看看。
另外有些文章講到的 BDD 都是在頁面操作上，但其實 BDD 的精神在於溝通，只是常見的範例都是講 UI 測試。在 UI 測試上我們是搭配 Page Object ，而以 BDD 實例來描述頁面功能，而不是頁面操作細節。

說真的，有時候技術裡面提到的好處，還是要看人們實際使用上時的默契。這點我們在實現時就已經有這樣的認知，所以會朝著兩邊都能夠願意花時間去找出契合點的這個方向來前進。

我們知道這很難一蹴可及，但還是試試看。把關鍵問題點找出來為止。

review 這件事就是大家一起討論，只要兩邊都覺得這樣看得懂就可以

---

規格的描述方式很重要，尤其是用 Gherkin 語法時， Given-When-Then 是讓 Production Owner 和 Programmer 之間溝通的橋樑，用詞要更為精準與易懂。

有時候會覺得自己寫得不難懂呀？但別人解讀時卻有誤會，這就表示。

---

本文提到有關 BDD 的反模式，像是寫完程式才開始寫場景、只由領域專家獨自建立場景、利用 UI 來執行場景等，這些模式在 BDD  過程中都該避免。

http://www.infoq.com/cn/news/2016/10/bdd-anti-patterns

本文介紹比 Page Objects 模式更注重 SOLID 原則的 Screenplay 模式，以頁面組件為重心來撰寫更為語意化的測試碼。

http://www.infoq.com/cn/articles/Beyond-Page-Objects-Test-Automation-Serenity-Screenplay



