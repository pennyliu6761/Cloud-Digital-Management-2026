# 📋 Week 12：專案管理自動化 — Notion Database 進階 × Make 整合

**雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系**

> **本週課程理念：** Week 04 我們用 Notion 架了一個對外網站。
> 但 Notion 還有一個更強大的用途——**管理複雜的專案和任務**。
>
> 期末專題還有三週，你的組員分工是否清楚？
> 截止日期有沒有人記得？進度落後了有沒有人知道？
>
> **今天我們用 Notion Database 的進階功能把期末專題管理起來，
> 再讓 Make 自動發送提醒，確保沒有任何任務被遺漏。**

---

## 🎯 本週學習目標

學完本週，你將能夠：
- ✅ 用 Notion Database 的 **Relations（關聯）** 連結兩張資料表
- ✅ 用 **Rollups（彙整）** 自動計算關聯資料的統計數字
- ✅ 用 **Formula（公式）** 建立自動計算的動態欄位
- ✅ 建立 **甘特圖視圖（Timeline View）**，視覺化專題時程
- ✅ 用 Make 監控 Notion Database，自動推播截止日提醒到 Discord

---

## 🏢 本週實作情境：【工管系期末專題管理中心】

> 💼 **情境：** 期末專題還有三週就要發表，
> 但各組都反映「不知道現在應該做什麼」、「忘記截止日期」、「不知道哪個任務卡住了」。
>
> **今天的任務：**
> 1. 建立 Notion 專題管理系統（任務看板 + 成員資料庫）
> 2. 用 Relations 把任務和成員連結起來
> 3. 用 Rollups 自動統計每位成員的任務完成率
> 4. 建立甘特圖視圖管理時程
> 5. Make 每天早上自動推播今天截止的任務到 Discord

---

## ⏰ 第 1 小時：Notion Database 進階 — Relations、Rollups、Formula

> 🎯 **本小時目標：** 學會 Notion Database 最強大的三個進階功能，
> 讓資料庫不只是「一張表」，而是「互相連結的智慧系統」。

---

### 💡 觀念一：為什麼需要 Relations？

Week 04 我們建立的 Notion Database 只有一張表。
但真實的專案管理需要多張表互相連結：
```
❌ 沒有 Relations 的做法：
任務資料庫：
  任務名稱 | 負責人（手動填文字）| 截止日期
  建立爬蟲  | 王大明              | 2026/05/01
  AI 分析  | 李美麗              | 2026/05/05

問題：
  → 王大明的 Email 打錯了，要逐筆修改
  → 無法統計每位成員有幾個任務
  → 無法看到某位成員所有負責的任務

✅ 有 Relations 的做法：
任務資料庫 ←→ 成員資料庫（兩張表互相連結）

任務資料庫：                成員資料庫：
  建立爬蟲 → [王大明] ←→   王大明（學號、Email、年級）
  AI 分析  → [李美麗] ←→   李美麗（學號、Email、年級）

優點：
  → 成員資料只存一份，Relations 自動連結
  → Rollup 可以統計每位成員的任務數和完成率
  → 點擊成員名稱可以直接看到他負責的所有任務
```

---

### 💡 觀念二：Rollups 和 Formula 的差異

| 功能 | 說明 | 範例 |
|------|------|------|
| **Rollup** | 從 Relations 的關聯資料計算統計值 | 「這位成員有幾個未完成任務」|
| **Formula** | 用公式計算同一列的其他欄位 | 「距離截止日還有幾天」|
```
Rollup（跨表計算）：
  成員資料庫 → 王大明 → 相關任務：[建立爬蟲, AI 分析, 錯誤處理]
  Rollup 計算：任務總數 = 3，已完成 = 1，完成率 = 33%

Formula（同列計算）：
  任務資料庫 → 建立爬蟲 → 截止日：2026/05/01
  Formula：距離截止日 = dateBetween(now(), prop("截止日期"), "days")
```

---

### 📌 任務 1-1：建立成員資料庫

1. 在 Notion 建立新頁面，命名：`📋 期末專題管理中心`
2. 輸入 `/database`，選「**Database - Inline**」
3. 命名：`👥 成員資料庫`
4. 建立以下欄位：

    | 欄位名稱 | 類型 | 說明 |
    |---------|------|------|
    | 姓名 | Title | 主欄位（必填）|
    | 學號 | Text | 七位數字 |
    | Email | Email | 聯絡信箱 |
    | 就讀年級 | Select | 二年級／三年級／四年級 |
    | 負責專題面向 | Multi-select | 前端／後端／資料分析／自動化／文件 |
    | Discord ID | Text | Discord 用戶名稱 |

5. 填入你們組的成員資料（3-4 筆）

<!-- 📸 截圖：成員資料庫建立完成，填入 3-4 筆成員資料 -->

---

### 📌 任務 1-2：建立任務資料庫

在同一個頁面，建立第二個 Database：

1. 輸入 `/database`，選「**Database - Inline**」
2. 命名：`✅ 任務資料庫`
3. 建立以下欄位：

    | 欄位名稱 | 類型 | 說明 |
    |---------|------|------|
    | 任務名稱 | Title | 主欄位 |
    | 狀態 | Select | 未開始／進行中／待審核／已完成 |
    | 優先程度 | Select | 🔴 緊急／🟠 高／🟡 中／⚪ 低 |
    | 截止日期 | Date | 任務截止日 |
    | 類別 | Select | 資料採集／AI 分析／自動化／視覺化／網站／文件 |
    | 說明 | Text | 任務詳細說明 |
    | 負責人 | Relation | **← 等等設定** |
    | 完成率 | Number | 0-100（手動填入進度百分比）|

4. 填入以下測試任務（至少 8 筆）：

    | 任務名稱 | 狀態 | 優先程度 | 截止日期 | 類別 |
    |---------|------|---------|---------|------|
    | ParseHub 爬蟲設定 | 已完成 | 🟠 高 | 2026/04/15 | 資料採集 |
    | Gemini AI 分析串接 | 已完成 | 🔴 緊急 | 2026/04/20 | AI 分析 |
    | Make 自動化劇本 | 進行中 | 🟠 高 | 2026/05/01 | 自動化 |
    | Looker Studio 儀表板 | 進行中 | 🟡 中 | 2026/05/05 | 視覺化 |
    | GitHub 網站建立 | 進行中 | 🟠 高 | 2026/05/08 | 網站 |
    | Notion 知識庫設計 | 未開始 | 🟡 中 | 2026/05/10 | 文件 |
    | 期末報告撰寫 | 未開始 | 🔴 緊急 | 2026/05/20 | 文件 |
    | 簡報製作 | 未開始 | 🟠 高 | 2026/05/22 | 文件 |
    | 系統壓力測試 | 未開始 | 🟡 中 | 2026/05/15 | 自動化 |
    | 成果影片錄製 | 未開始 | 🟡 中 | 2026/05/18 | 文件 |

<!-- 📸 截圖：任務資料庫填入 10 筆任務 -->

---

### 📌 任務 1-3：設定 Relations（關聯）

把任務資料庫的「負責人」欄位連結到成員資料庫：

1. 點擊任務資料庫的「**負責人**」欄位標題 → 「**Edit property**」
2. 類型選「**Relation**」
3. 「Relation to」選擇「**👥 成員資料庫**」
4. 開啟「**Show on 👥 成員資料庫**」，命名為「負責任務」
5. 點擊「**Save**」

現在為每筆任務指定負責人：
點擊任務列的「負責人」欄位 → 從成員清單選擇對應的成員。

<!-- 📸 截圖：Relations 設定完成，任務資料庫的負責人欄位顯示成員名稱 -->

> [!NOTE]
> **雙向連結的效果：**
> 設定完 Relations 後，回到成員資料庫，
> 每位成員的「負責任務」欄位會自動顯示他們被指派的所有任務。
> 這就是 Relations 的力量——在一個地方設定，兩邊都能看到。

<!-- 📸 截圖：成員資料庫的「負責任務」欄位自動顯示關聯的任務 -->

---

### 📌 任務 1-4：設定 Rollups（彙整）

在成員資料庫新增 Rollup 欄位，自動統計每位成員的任務情況：

**Rollup 1：任務總數**

1. 在成員資料庫點擊「**+**」新增欄位
2. 類型選「**Rollup**」
3. 設定：
    - Relation：「負責任務」
    - Property：「任務名稱」
    - Calculate：**Count（計數）**
4. 命名：`任務總數`

**Rollup 2：已完成任務數**

1. 再新增一個 Rollup 欄位
2. 設定：
    - Relation：「負責任務」
    - Property：「狀態」
    - Calculate：**Count values（計數符合條件的值）**
    - Filter：狀態 = 已完成
3. 命名：`已完成任務數`

**Rollup 3：最近截止日**

1. 再新增一個 Rollup 欄位
2. 設定：
    - Relation：「負責任務」
    - Property：「截止日期」
    - Calculate：**Earliest date（最早日期）**
3. 命名：`最近截止任務日`

<!-- 📸 截圖：成員資料庫加入三個 Rollup 欄位後的完整畫面 -->

---

### 📌 任務 1-5：設定 Formula（公式）

在任務資料庫新增 Formula 欄位，自動計算動態數值：

**Formula 1：距離截止日天數**

1. 在任務資料庫新增欄位，類型選「**Formula**」
2. 公式：
```
    dateBetween(prop("截止日期"), now(), "days")
```

3. 命名：`距截止日（天）`

> [!NOTE]
> 這個公式計算「截止日期 - 今天」的天數。
> 正數 = 還有幾天，負數 = 已經過期幾天。

**Formula 2：任務狀態標籤（含顏色 emoji）**
```
if(prop("狀態") == "已完成", "✅ 完成",
  if(prop("距截止日（天）") < 0, "🔴 已逾期",
  if(prop("距截止日（天）") <= 3, "🟠 即將到期",
  if(prop("狀態") == "未開始", "⚪ 未開始",
  "🔵 進行中"))))
```

命名：`緊急程度標籤`

**Formula 3：完成率顯示（進度條文字）**
```
if(prop("完成率") == 100, "██████████ 100%",
  if(prop("完成率") >= 80, "████████░░ " + format(prop("完成率")) + "%",
  if(prop("完成率") >= 60, "██████░░░░ " + format(prop("完成率")) + "%",
  if(prop("完成率") >= 40, "████░░░░░░ " + format(prop("完成率")) + "%",
  if(prop("完成率") >= 20, "██░░░░░░░░ " + format(prop("完成率")) + "%",
  "░░░░░░░░░░ " + format(prop("完成率")) + "%")))))
```

命名：`進度條`

<!-- 📸 截圖：任務資料庫加入三個 Formula 欄位，顯示距截止日天數和進度條 -->

> [!TIP]
> **🏆 第 1 小時 Checkpoint 完成！**
>
> - ✅ 建立成員資料庫和任務資料庫兩張互相關聯的表
> - ✅ 設定 Relations 連結任務和負責人（雙向可見）
> - ✅ 設定三個 Rollups 自動統計每位成員的任務情況
> - ✅ 設定三個 Formulas 計算截止日倒數、緊急程度和進度條

---

### 🎯 第 1 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 1-A】新增 Rollup 欄位（基礎）

在成員資料庫新增第四個 Rollup：

- 欄位名稱：`平均完成率`
- 設定：Relation「負責任務」→ Property「完成率」→ Calculate「Average（平均值）」

截圖成員資料庫，顯示四個 Rollup 欄位的完整畫面。

---

#### 【練習 1-B】新增 Formula 欄位（進階）

在任務資料庫新增一個 Formula，計算任務的「實際工作天數」
（從今天到截止日，但排除週末）：

Notion Formula 目前沒有直接排除週末的函數，
請設計一個「近似版」公式：
```
round(dateBetween(prop("截止日期"), now(), "days") * (5/7))
```

解釋為什麼這個公式是「近似值」而不是精確值，
並說明在什麼情況下這個近似值會出現較大的誤差。

---

#### 【練習 1-C】觀念問答（思考題）

請回答：

> ❓ Notion Database 的 Relations 和 Week 06 學的
> Google Sheets 多表關聯（用 VLOOKUP 連結）有什麼相似和不同之處？
>
> 請從以下三個角度比較：
> 1. 建立關聯的方式（操作複雜度）
> 2. 查詢關聯資料的方式（讀取資料的靈活性）
> 3. 適合的使用情境（什麼時候用 Notion，什麼時候用 Sheets）

---

## ⏰ 第 2 小時：多種視圖設計 + 甘特圖時程管理

> 🎯 **本小時目標：** 用 Notion Database 的不同視圖，
> 讓同一份資料以最適合的方式呈現給不同需求的人。

---

### 💡 觀念：同一份資料，多種視圖

Notion Database 的精髓在於：
**資料只存一份，但可以用完全不同的視圖來呈現。**
```
任務資料庫（同一份資料）
    ├── Table View    → 逐列顯示所有欄位（適合資料輸入）
    ├── Board View    → 依狀態分欄看板（適合追蹤進度）
    ├── Timeline View → 甘特圖時程（適合管理截止日）
    ├── Calendar View → 月曆視圖（適合看哪天有任務到期）
    └── Gallery View  → 卡片視圖（適合視覺化展示）
```

---

### 📌 任務 2-1：建立 Board View（看板視圖）

1. 點擊任務資料庫右上角「**+ Add a view**」
2. 選擇「**Board**」
3. 命名：`📊 進度看板`
4. Group by（分組依據）：選「**狀態**」
5. 設定欄的順序：未開始 → 進行中 → 待審核 → 已完成

**樣式設定：**
1. 點擊每個卡片的設定，顯示以下欄位：
    - 負責人
    - 截止日期
    - 緊急程度標籤（Formula 欄位）
2. 開啟「**Color columns**」，讓不同狀態的欄有不同顏色

<!-- 📸 截圖：Board View 看板，依狀態分成四欄，卡片顯示負責人和截止日 -->

---

### 📌 任務 2-2：建立 Timeline View（甘特圖）

1. 點擊「**+ Add a view**」→ 選「**Timeline**」
2. 命名：`📅 專題時程甘特圖`
3. 設定：
    - Date property：「截止日期」
    - Show table：開啟（左側顯示任務清單，右側顯示時程條）
4. 在左側顯示欄位：任務名稱、狀態、負責人、優先程度

**甘特圖視覺化設定：**
1. 點擊右上角「**Group**」→ 依「**類別**」分組
2. 點擊「**Color**」→ 依「**狀態**」上色：
    - 已完成：綠色
    - 進行中：藍色
    - 未開始：灰色
    - 待審核：橘色

<!-- 📸 截圖：Timeline 甘特圖視圖，顯示所有任務的時程條，依類別分組 -->

---

### 📌 任務 2-3：建立 Calendar View（月曆視圖）

1. 點擊「**+ Add a view**」→ 選「**Calendar**」
2. 命名：`📆 截止日月曆`
3. Date property：「截止日期」
4. 確認五月份有任務顯示在對應的日期格

> [!NOTE]
> **Calendar View 的用途：**
> 讓你一眼看出哪幾天的任務特別密集（截止日堆在一起），
> 可以提前安排工作節奏，避免最後一週什麼都卡在一起。

<!-- 📸 截圖：Calendar View，顯示五月份的任務截止日分布 -->

---

### 📌 任務 2-4：建立成員任務分配視圖

在任務資料庫新增一個特殊視圖，讓每位成員一目了然地看到自己的任務：

1. 點擊「**+ Add a view**」→ 選「**Board**」
2. 命名：`👥 成員任務分配`
3. Group by：選「**負責人**」（按成員分欄）
4. Filter：狀態 ≠ 已完成（只顯示未完成的任務）
5. Sort：截止日期由近到遠

這樣每個成員的欄位就只顯示他還沒完成的任務，並按截止日排序。

<!-- 📸 截圖：成員任務分配視圖，每個成員一欄，顯示未完成任務 -->

---

### 📌 任務 2-5：建立專題儀表板頁面

在「📋 期末專題管理中心」頁面最上方，
新增一個儀表板區塊，彙整關鍵資訊：

使用 Notion 的 **Column Layout（多欄排版）** 建立以下區塊：

**左欄（1/3 寬）— 快速統計：**

用 `/callout` Block 手動填入統計數字（或用 Linked Database 顯示）：
```
📊 專題進度總覽

任務總數：10 個
✅ 已完成：2 個
🔵 進行中：3 個
⚪ 未開始：5 個
完成率：20%

⏰ 本週截止任務：
· Make 自動化劇本（05/01）
· Looker Studio 儀表板（05/05）
```

**中欄（1/3 寬）— 快速連結：**
```
🔗 重要連結

📁 GitHub Repository
🌐 Vercel 網站
📊 Looker Studio 儀表板
🕵️ 情報知識庫
📅 甘特圖視圖
```

**右欄（1/3 寬）— 本週重點：**
```
🎯 本週重點任務

1. Make 劇本完成（王大明負責）
2. 儀表板第一版（李美麗負責）
3. 網站內容填入（全組）

下次會議：2026/05/03 晚上 8:00
```

<!-- 📸 截圖：專題管理中心頁面，顯示儀表板區塊和下方的兩個資料庫 -->

> [!TIP]
> **🏆 第 2 小時 Checkpoint 完成！**
>
> - ✅ Board View：依狀態分欄的進度看板
> - ✅ Timeline View：甘特圖時程，依類別分組並上色
> - ✅ Calendar View：截止日月曆，一眼看出密集截止日
> - ✅ 成員任務分配視圖：每位成員的未完成任務一目了然
> - ✅ 專題儀表板頁面：彙整進度統計和重要連結

---

### 🎯 第 2 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人作業**

---

#### 【練習 2-A】新增篩選條件（基礎）

在甘特圖視圖（Timeline View）新增篩選條件：

1. 篩選器 1：只顯示「未開始」和「進行中」的任務（排除已完成）
2. 篩選器 2：只顯示「優先程度」為「🔴 緊急」或「🟠 高」的任務

設定完成後，確認甘特圖只顯示需要關注的高優先任務，截圖結果。

---

#### 【練習 2-B】Linked Database（進階）

Notion 允許你在不同頁面「引用」同一個 Database，
而不是複製一份。

1. 在你的 GitHub 網站（Week 11 的 Notion 頁面）新增一個子頁面
2. 輸入 `/linked`，選擇「**Linked view of database**」
3. 選擇「✅ 任務資料庫」
4. 建立一個只顯示「已完成」任務的 Table View
5. 命名：「✅ 已完成里程碑」

這樣你的 GitHub 網站就有一個「成果展示」頁面，
自動同步顯示已完成的所有任務。

截圖 Linked Database 在另一個頁面正確顯示的畫面。

---

#### 【練習 2-C】觀念問答（思考題）

請回答：

> ❓ 本週的 Notion 專案管理系統和業界常用的
> Jira、Trello、Asana 等專案管理工具相比，
> 有哪些優勢和哪些明顯的不足？
>
> 如果你畢業後進入一家使用 Jira 的企業，
> 這週學到的「Relations、Rollups、Formula」概念，
> 在 Jira 中有對應的功能嗎？
> （請自行簡單查詢 Jira 的功能後回答）

---

## ⏰ 第 3 小時：Make 整合 — 自動截止日提醒 + GitHub 同步

> 🎯 **本小時目標：** 用 Make 讓 Notion 專案管理系統主動通知組員，
> 確保沒有任何任務被遺忘，並把 GitHub 的 Commit 記錄同步到 Notion。

---

### 💡 觀念：Notion API 的讀取和寫入

上週（Week 10）我們用 Notion API **寫入**（建立新頁面）。
本週我們需要 **讀取**（查詢符合條件的資料庫記錄），
用這個讀取結果觸發 Discord 通知。
```
Week 10：Make → Notion API（寫入）
  HTTP POST /v1/pages → 在 Database 建立新頁面

Week 12：Make → Notion API（讀取）
  HTTP POST /v1/databases/{id}/query → 查詢符合條件的頁面
```

---

### 📌 任務 3-1：建立截止日提醒劇本

**劇本架構：**
```
[Schedule 每天早上 8:00]
    ↓
[HTTP POST → Notion API 查詢今天截止的任務]
    ↓
[JSON Parse → 解析查詢結果]
    ↓
[Iterator → 逐筆處理每個任務]
    ↓
[Router]
    ├→ 今天截止 → Discord #今日截止任務
    └→ 明天截止 → Discord #明日截止任務
    └→ 已逾期   → Discord #逾期警報
```

---

#### 節點一：Schedule 觸發器

每天早上 8:00 執行。

---

#### 節點二：Notion API 查詢今天截止的任務

**取得 Database ID：**

從任務資料庫的 URL 複製 Database ID（32 位字元的亂數字串）。

**HTTP POST 呼叫 Notion Database Query API：**

1. 新增「**HTTP → Make a request**」
2. 設定：

    | 設定項目 | 填入內容 |
    |---------|---------|
    | URL | `https://api.notion.com/v1/databases/你的DatabaseID/query` |
    | Method | `POST` |

3. Headers：

    | Key | Value |
    |-----|-------|
    | `Authorization` | `Bearer secret_你的NotionKey` |
    | `Notion-Version` | `2022-06-28` |
    | `Content-Type` | `application/json` |

4. Body（JSON）— 查詢截止日在今天到明天之間的任務：
```json
    {
      "filter": {
        "and": [
          {
            "property": "截止日期",
            "date": {
              "on_or_before": "{{formatDate(addDays(now(); 1); \"YYYY-MM-DD\")}}"
            }
          },
          {
            "property": "狀態",
            "select": {
              "does_not_equal": "已完成"
            }
          }
        ]
      },
      "sorts": [
        {
          "property": "截止日期",
          "direction": "ascending"
        }
      ]
    }
```

5. **Parse response** → `Yes`

> [!NOTE]
> **這個 Query 做什麼：**
> 查詢所有「截止日期 ≤ 明天」AND「狀態 ≠ 已完成」的任務，
> 按截止日期升序排列（最緊急的在最前面）。

<!-- 📸 截圖：HTTP 模組設定，顯示 Notion Query API 的 Body -->

---

#### 節點三：JSON Parse

解析 Notion API 回傳的結果，
Notion 的 Response 結構比較複雜：
```json
{
  "results": [
    {
      "id": "頁面ID",
      "properties": {
        "任務名稱": {
          "title": [{"plain_text": "Make 自動化劇本"}]
        },
        "截止日期": {
          "date": {"start": "2026-05-01"}
        },
        "狀態": {
          "select": {"name": "進行中"}
        },
        "負責人": {
          "relation": [{"id": "成員頁面ID"}]
        }
      }
    }
  ]
}
```

在 JSON Parse 後，你需要設定正確的路徑取出各欄位。

---

#### 節點四：Iterator 逐筆處理

把 `results` 陣列用 Iterator 拆開。

---

#### 節點五：Tools — Set Variable 整理欄位

Notion 的 Property 結構很深，先用 Set Variable 整理成易用的變數：

| 變數名稱 | 值 |
|---------|---|
| `task_name` | `{{value.properties.任務名稱.title[].plain_text}}` |
| `due_date` | `{{value.properties.截止日期.date.start}}` |
| `status` | `{{value.properties.狀態.select.name}}` |
| `days_left` | `{{dateDifference(value.properties.截止日期.date.start; now(); "days")}}` |

---

#### 節點六：Router 分流

根據 `days_left` 的值分流：

**支線 1 — 已逾期（days_left < 0）：**

Filter：`days_left` Less than `0`

Discord 訊息（推播到 `#逾期警報`）：
```
🔴 **【任務逾期警報】**

▸ **任務：** {{task_name}}
▸ **原截止日：** {{due_date}}
▸ **已逾期：** {{abs(days_left)}} 天
▸ **狀態：** {{status}}

⚠️ 請立即處理或與組長說明延遲原因。
```

**支線 2 — 今天截止（days_left = 0）：**

Filter：`days_left` Equal to `0`

Discord 訊息（推播到 `#今日截止任務`）：
```
🟠 **【今日截止任務提醒】**

▸ **任務：** {{task_name}}
▸ **截止日：** 今天（{{due_date}}）
▸ **狀態：** {{status}}

請確認今天完成並更新 Notion 狀態。
```

**支線 3 — 明天截止（days_left = 1）：**

Filter：`days_left` Equal to `1`

Discord 訊息（推播到 `#明日截止任務`）：
```
🟡 **【明日截止任務預告】**

▸ **任務：** {{task_name}}
▸ **截止日：** 明天（{{due_date}}）
▸ **狀態：** {{status}}

明天截止，請確認進度是否順利。
```

<!-- 📸 截圖：Make 完整劇本畫布，顯示 Notion 查詢到 Router 三路分流 -->

---

### 📌 任務 3-2：建立每週進度彙整劇本

除了每天的截止日提醒，每週一發送一次整體進度彙整：

**劇本架構（簡化版）：**
```
[Schedule 每週一早上 9:00]
    ↓
[HTTP POST → Notion API 查詢所有任務]
    ↓
[Text Aggregator → 彙整所有任務狀態]
    ↓
[HTTP POST → Gemini 生成週報摘要]
    ↓
[Discord 推播到 #每週進度報告]
```

**Gemini Prompt（生成週報）：**
```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "你是一位專案管理顧問。\n以下是一個工管系期末專題的任務清單，請生成一份簡潔的週進度報告。\n\n格式要求：\n1. 本週進度摘要（2-3句話）\n2. 進行順利的項目（列點）\n3. 需要關注的風險（列點）\n4. 下週重點建議（2-3個行動建議）\n\n全文繁體中文，不超過 300 字。\n\n任務清單：\n{{aggregated_tasks}}"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.3,
    "maxOutputTokens": 500
  }
}
```

**Discord 週報格式：**
```
📊 **【期末專題週進度報告】{{formatDate(now(); "YYYY/MM/DD")}}**

{{ai_weekly_report}}

---
📈 **本週數據：**
▸ 任務完成率：{{completion_rate}}%
▸ 本週新完成：{{newly_completed}} 個
▸ 下週截止任務：{{next_week_due}} 個

🔗 Notion 專案管理中心：{{notion_url}}
```

<!-- 📸 截圖：Discord #每週進度報告 收到 AI 生成的週報 -->

---

### 📌 任務 3-3：當 Notion 任務狀態更新時自動通知

當組員把任務狀態從「進行中」改為「已完成」，
自動發 Discord 慶祝通知：

**觸發方式：** 用 Make 的 Notion Trigger（Watch Database Items）

> [!NOTE]
> **Make 有原生的 Notion 模組：**
> 不需要自己呼叫 HTTP API，Make 的 App 清單裡直接有 Notion，
> 包含「Watch Database Items」觸發器。

1. 新建劇本，觸發器選「**Notion → Watch Database Items**」
2. 連結你的 Notion 帳號（OAuth 授權）
3. 選擇「✅ 任務資料庫」

> [!WARNING]
> **注意：Make 的 Notion Watch 模組**
> 監控的是「有任何變更的項目」，不只是新增的項目。
> 需要在後面加 Filter 篩選出「狀態 = 已完成」的變更。

4. 加入 **Filter**：
    - 條件：`status.name` Equal to `已完成`

5. Discord 推播到 `#今日截止任務`：
```
    🎉 **【任務完成！】**

    ✅ **{{task_name}}** 已標記為完成！

    負責人辛苦了！繼續加油 💪
    距離期末發表還有 {{days_to_final}} 天。
```

<!-- 📸 截圖：在 Notion 把任務標記為完成，Discord 自動收到慶祝通知 -->

> [!TIP]
> **🏆 第 3 小時 Checkpoint 完成！**
>
> - ✅ 建立每日截止日提醒劇本（Notion Query API → Router 三路分流）
> - ✅ 建立每週進度彙整劇本（Aggregator + Gemini 週報 + Discord）
> - ✅ 建立任務完成自動慶祝通知（Notion Watch → Discord）

---

### 🎯 第 3 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人 + 分組**

---

#### 【練習 3-A】個人任務提醒（個人，基礎）

修改截止日提醒劇本，讓每個任務的通知同時 @mention 負責人的 Discord 帳號：

在 Discord 訊息中加入：
```
👤 **負責人：** <@{{discord_user_id}}>
```

> [!TIP]
> Discord 的 @mention 格式是 `<@使用者ID>`。
> 使用者 ID 可以在 Discord 開啟開發者模式後，右鍵點擊使用者取得。
> 把每位組員的 Discord 使用者 ID 填入 Notion 成員資料庫的「Discord ID」欄位，
> 再用 Notion API 查詢時一併取出。

截圖 Discord 收到帶有 @mention 的截止日提醒。

---

#### 【練習 3-B】整合挑戰（分組，2～3 人）

**建立你們組的完整「期末專題自動化管理系統」：**

需求：
1. Notion 專題管理中心，包含：
    - 成員資料庫（含 Relations、Rollups）
    - 任務資料庫（含 Formula、四種視圖）
    - 專題儀表板頁面
2. Make 三個劇本：
    - 每天 8:00 截止日提醒（三路分流）
    - 每週一 9:00 進度週報（Gemini 生成）
    - 任務完成自動慶祝通知
3. 把「✅ 已完成里程碑」Linked Database 嵌入到 Week 11 的 GitHub 網站

繳交：
- Notion 專案管理中心的截圖（含甘特圖視圖）
- Make 三個劇本的截圖
- Discord 收到至少一則提醒和一則週報的截圖
- GitHub 網站嵌入已完成任務的截圖

---

#### 【練習 3-C】期末專題最終確認（重要）

> ❓ **距離期末發表（Week 14）還有兩週，請確認以下事項：**
>
> 1. **你的期末專題題目已確定**：
>    用一句話說明「這個系統解決了什麼人的什麼問題」
>
> 2. **你的系統用到了哪些工具**（在本學期學過的）：
>    列出至少 5 個工具，說明每個工具在系統中的角色
>
> 3. **你的 GitHub 展示網站已建立**（Week 11 建立的）：
>    貼出 Vercel 網址
>
> 4. **期末 10 分鐘報告的大綱**：
>    - 2 分鐘：問題說明
>    - 3 分鐘：系統架構和工具展示
>    - 3 分鐘：成果展示（Live Demo 或截圖）
>    - 2 分鐘：學到什麼、未來改進方向
>
> 把以上四點貼到課堂共用文件，下週老師會逐組確認。

---

## 🏆 本週總結

### 📊 本週技能地圖
```
【Notion Database 進階】
    ├── Relations：兩張表互相連結（任務 ↔ 成員）
    ├── Rollups：從關聯資料計算統計值（任務數、完成率、最近截止日）
    ├── Formula：同列欄位計算（截止日倒數、緊急程度標籤、進度條）
    └── 多種視圖：Table、Board、Timeline（甘特圖）、Calendar、Gallery

        ↓ 視覺化管理

【Make 整合 Notion】
    ├── Notion Database Query API：查詢符合條件的頁面
    ├── Make Notion Watch 模組：監控 Database 變更
    ├── 截止日三路分流提醒：逾期/今天/明天
    ├── Gemini 生成每週進度週報
    └── 任務完成自動慶祝通知

        ↓ 自動化管理

【系統整合】
    ├── Notion 知識庫 ← Week 10 情報局
    ├── GitHub 網站 ← Week 11 架站
    └── Linked Database：跨頁面引用同一份資料
```

### 🔄 本週技術的職場對應
```
本週學的               對應的業界工具
────────────────────────────────────
Notion Database      →  Jira、Asana、Monday.com
Relations            →  資料庫外鍵（Foreign Key）
Rollups              →  SQL 的 GROUP BY + 聚合函數
Formula              →  Excel 的 IF、DATEDIF
Timeline View        →  Microsoft Project 甘特圖
Make Watch           →  IFTTT、Zapier 的觸發器
```

### 🤔 課後思考問題

1. **專案管理的本質：** Notion 幫你「記錄」任務，
   Make 幫你「提醒」截止日，
   但真正讓專題成功的關鍵是什麼？
   技術工具能解決的問題和無法解決的問題各是什麼？

2. **Formula 的限制：** Notion Formula 的功能比 Google Sheets 的公式弱很多，
   例如它沒有 VLOOKUP、QUERY 等強大函數。
   這說明了什麼？工具的選擇應該根據什麼標準？

3. **自動化的反效果：** 你設定了每天早上 8:00 截止日提醒，
   如果組員每天都收到很多提醒但沒有行動，
   最終可能產生「通知疲勞」，反而開始忽略所有通知。
   你會如何設計提醒系統，讓它「有用但不煩人」？

### 📚 本週自學資源

| 資源 | 用途 |
|------|------|
| [Notion Formula 參考](https://www.notion.so/help/formulas) | 所有可用的 Formula 函數 |
| [Notion API Database Query](https://developers.notion.com/reference/post-database-query) | Query API 完整語法 |
| [Make Notion 模組說明](https://www.make.com/en/help/app/notion) | Make 原生 Notion 模組使用方式 |
| [甘特圖設計最佳實踐](https://asana.com/resources/gantt-chart) | 甘特圖的設計和使用原則 |

---

### 📝 下週預告

> 🔜 **Week 13：雲端協作整合 — Slack × Make × Canva 圖文自動推播**
>
> - Slack 入門：Workspace、Channel、Webhook 設定
> - Slack vs Discord：什麼情況用哪個？
> - Canva API：自動生成圖文卡片（帶資料的海報）
> - Make 整合：定時把 Notion 情報 + Canva 圖卡推播到 Slack
> - 模擬企業的「雲端協作工作流」：從任務到通知到確認
>
> **課前任務：**
> 1. 建立 [Slack](https://slack.com/) 帳號（免費）
> 2. 建立一個 Slack Workspace，命名為「工管系 2026 春季」
> 3. 邀請至少一位同學加入你的 Workspace（需要對方的 Email）

---

*雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系 ｜ 2026 春季學期*
*Week 12 of 14*
