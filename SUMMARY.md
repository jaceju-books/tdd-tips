# Summary

* [簡介](README.md)
* 物件導向
  * [為什麼需要物件導向？](object-oriented/index.md)
    * 無法從程序化的思維轉換到物件導向的思維
    * 不瞭解物件導向帶給你什麼好處
    * 物件導向很難寫出好維護的程式碼
    * 開始有什麼需求都該用物件導向來完成的迷思
  * 如何掌握物件導向的精神？
    * 如何快速向別人圖解一個概念 - 抽象
    * 如何向別人描述一個行為 - 封裝
    * 相同又有不同 - 繼承
    * 當同一個指令有不同做法 - 多型
  * 新手常犯的錯誤有哪些？
    * 萬能類別或方法
    * 不必要的曝露
    * 把方法當成函式來用
    * 無法分辨抽象與實作
    * 不良的方法命名
    * 搞混類別與物件的使用時機
    * 順便做點什麼
    * 錯誤的繼承關係
  * 該怎麼著手設計物件導向的程式？
    * 從需求出發
    * 注意靜態方法與函式的使用時機
    * 內聚 (Cohesion)
    * 耦合 (Coupling)
    * 繼承設計
    * 常見的多型物件來源
    * 在關鍵點切開職責
    * 越早判斷，越不容易發散
  * SOLID 原則
    * SRP
    * OCP
    * LSP
    * LKP
    * ISP
    * DIP
  * 設計模式
    * 工廠方法
    * 簡單工廠
    * 抽象工廠
  * 分層設計
  * 範例
    * Intervention Image
* [測試](/testing/index.md)
  * 打破你對測試的舊有思維
  * 怎樣才能算是好的自動化測試
  * 利用假物件來測試
* [重構](/refactoring/index.md)
  * [分析程式碼品質](/refactoring/qa-tools.md)
  * [重構判斷式](/refactoring/refactor-if-statement.md)
    * [將一連串的判斷式重構成責任鍊模式](/refactoring/refactor-if-else-to-cor.md)
  * [重構 PHP 不良語法](/refactoring/refactor-bad-php-code.md)
    * [重構 extract 與 compact](/refactoring/refactor-extract-compact.md)
