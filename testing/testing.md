# TDD/BDD

1. TDD 不是沒有設計
2. Baby Steps - 不必過度擔心後面需求的變動
    1. 列出系統所有 User Stories
        1. 設計時可以全局考量
    2. 建立 Features
    3. 一次只做一個 Feature
    4. 當需求改變時，還沒做的 Feature 可以被捨棄
3. 三天的流程總結
    1. 需求分析
        1. 寫出 User Stories
        2. 寫出 Features
        3. 決定 Feature 的優先順序
    2. 系統初步設計
        1. 系統應該有哪些模組
        2. 哪些模組可以並行
        3. 討論 Scenarios
    3. 系統細部設計
        1. 類別 API 的設計
        2. 類別間如何互動
        3. 完成 Scenarios 細節
    4. 進入開發
        1. 加入第一個 Scenario
        2. 轉換成 Feature Context
        3. 依 3A 原則在 Context 撰寫測試
        4. 寫出能讓測試通過的程式碼
        5. 提交
        6. 重構並保持測試通過
        7. 下一個 Scenario
    5. 完成 Feature
    6. 檢討 Feature / Scenario 的方向
        1. 如果有更動就留到下一個 phase


