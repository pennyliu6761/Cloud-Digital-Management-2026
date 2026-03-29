# 🌐 Week 11：AI 架站 — GitHub + Vercel + AI 生成互動網頁

**雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系**

> **本週課程理念：** Week 04 我們用 Notion 做了一個簡單的靜態網站——
> 內容是死的，沒有互動，樣式受限。
>
> 今天我們要做一個真正的網站：
> **你用中文描述需求，AI 幫你寫程式，GitHub 管理版本，Vercel 一鍵上線。**
> 不需要懂 HTML、CSS、JavaScript，但你做出來的網站和工程師寫的一樣真實。
>
> **更重要的是——這個網站會成為你期末專題的展示平台。**

---

## 🎯 本週學習目標

學完本週，你將能夠：
- ✅ 理解 GitHub 的核心概念（Repository、Commit、Push），並完成基本操作
- ✅ 用 Google AI Studio 描述需求，讓 AI 生成完整的 HTML/CSS/JS 網頁
- ✅ 把 AI 生成的程式碼上傳到 GitHub，觸發 Vercel 自動部署
- ✅ 修改網頁內容（文字、顏色、結構），不需要懂程式語法
- ✅ 建立一個有互動圖表、動態效果的個人專題展示網站

---

## 🏢 本週實作情境：【工管系期末專題展示網站】

> 💼 **情境：** 學期末，每組同學要在系上的「成果發表會」展示專題。
> 過去都是用 PowerPoint，但今年系主任希望每組都有一個**對外公開的網站**，
> 讓企業合作夥伴可以透過網址直接瀏覽各組的成果。
>
> **今天的任務：**
> 1. 建立 GitHub Repository 作為網站的程式碼倉庫
> 2. 用 AI Studio 生成個人專題展示頁面的 HTML
> 3. 透過 Vercel 自動部署，取得公開網址
> 4. 學會修改和更新網站內容
> 5. 把前幾週的成果（Looker Studio、Notion 等）整合進網站

---

## ⏰ 第 1 小時：GitHub 基礎 + AI Studio 生成網頁

> 🎯 **本小時目標：** 完成 GitHub 的基本操作流程，
> 並用 AI Studio 生成你的第一個真實網頁。

---

### 💡 觀念一：GitHub 是什麼？

**GitHub** 是一個「程式碼的 Google Drive」——
但比 Google Drive 更強大，它記錄每一次的修改歷史。
````
Google Drive 的做法：
  report_v1.docx
  report_v2.docx
  report_v3_final.docx
  report_v3_final_真的最終版.docx  ← 你一定遇過這個

GitHub 的做法：
  report.docx（只有一個檔案）
  └── 版本 1：2026/03/01 初稿
  └── 版本 2：2026/03/05 加入圖表
  └── 版本 3：2026/03/10 修改結論
  └── 版本 4：2026/03/15 最終版
  可以隨時回到任何一個版本
````

**三個核心概念：**

| 名詞 | 白話說明 | 類比 |
|------|---------|------|
| **Repository（倉庫）** | 一個專案的所有檔案和歷史記錄 | Google Drive 的一個資料夾 |
| **Commit（提交）** | 一次儲存記錄，附帶說明訊息 | 按下「儲存」並寫下「這次改了什麼」|
| **Push（推送）** | 把本機的變更上傳到 GitHub | 把本機的資料夾同步到雲端 |

---

### 💡 觀念二：Week 04 vs Week 11 的架站差異

| 比較項目 | Week 04 Notion Sites | Week 11 GitHub + Vercel |
|---------|---------------------|------------------------|
| 技術難度 | 低（拖拉介面）| 中（需要 AI 生成程式碼）|
| 樣式自由度 | 低（受 Notion 限制）| 高（完全自訂）|
| 互動功能 | 有限（只能嵌入）| 無限（可以寫任何 JS 效果）|
| 網址格式 | `notion.site/xxx` | 自訂（`xxx.vercel.app`）|
| 程式碼控制 | 無 | 完整 Git 版本控制 |
| 適合情境 | 快速發布說明頁面 | 專業的作品集和展示網站 |
| 更新方式 | 直接編輯 Notion | 修改程式碼 → Commit → 自動部署 |

---

### 📌 任務 1-1：建立 GitHub Repository

> [!NOTE]
> **課前任務確認：** 你應該已經建立好 GitHub 帳號，
> 並建立了名為 `my-portfolio` 的 Repository。
> 如果還沒有，現在進行。

1. 登入 [github.com](https://github.com/)
2. 點擊右上角「**+**」→「**New repository**」
3. 設定：

    | 設定項目 | 填入內容 |
    |---------|---------|
    | Repository name | `ie-portfolio`（工管系作品集）|
    | Description | `國立金門大學工管系 雲端與數位內容管理 期末成果展示` |
    | Visibility | **Public**（Vercel 免費版需要 Public）|
    | Add a README file | ✅ 勾選 |

4. 點擊「**Create repository**」

<!-- 📸 截圖：GitHub 建立 Repository 設定畫面 -->
<!-- 📸 截圖：Repository 建立完成，顯示初始的 README.md -->

---

### 📌 任務 1-2：在 Google AI Studio 生成網頁

1. 前往 [Google AI Studio](https://aistudio.google.com/)
2. 點擊「**Create new prompt**」→ 選擇「**Stream realtime**」
3. 右側 Model 選擇「**Gemini 2.0 Flash**」
4. 在輸入框貼入以下 Prompt：
````
    請幫我生成一個完整的 HTML 網頁，作為工管系學生的期末專題展示頁面。

    【技術要求】
    - 單一 HTML 檔案，CSS 和 JavaScript 都寫在同一個檔案裡
    - 不使用任何外部框架（不要 React、Vue 等），只用原生 HTML/CSS/JS
    - 可以使用 CDN 引入 Chart.js 做圖表
    - 需要有 RWD（響應式設計），在手機上也要好看

    【頁面結構】
    1. 導覽列（Navigation）：
       - 左側：「工管系期末成果展示」標題
       - 右側：首頁、系統介紹、成果展示、團隊成員 四個錨點連結

    2. 首頁 Hero 區塊：
       - 大標題：「工管系廠商實習處理中心」
       - 副標題：「整合 AI × 自動化 × 雲端的智慧實習管理系統」
       - 兩個按鈕：「查看成果」（連到成果展示區）和「了解系統」（連到系統介紹區）
       - 背景用漸層色（深藍色到靛藍色）

    3. 系統介紹區塊：
       - 標題：「我們解決了什麼問題」
       - 三個卡片，分別介紹：
         a. 問題：「過去實習申請全靠紙本，每學期浪費 200 人小時」
         b. 解法：「整合 Google 表單、Make、Gemini AI，實現全流程自動化」
         c. 成果：「申請處理時間從 3 天縮短為 3 分鐘，準確率 99%」
       - 卡片要有 hover 效果（滑鼠移上去時稍微放大）

    4. 成果展示區塊：
       - 標題：「系統成果數據」
       - 用 Chart.js 做兩個圖表：
         a. 長條圖：「各週實習申請人數」（x 軸：Week 1-8，y 軸：申請人數，使用假資料）
         b. 圓餅圖：「實習類型分布」（國內企業 60%、海外實習 25%、自行開發 15%）
       - 圖表下方放兩個統計數字：「本學期申請總數：127 件」和「自動化節省時間：381 小時」

    5. 工具技術堆疊區塊：
       - 標題：「使用的工具與技術」
       - 用 6 個圖示卡片顯示：Google Forms、Make、Gemini AI、Notion、ParseHub、Looker Studio
       - 每個卡片有 emoji 圖示、工具名稱、一句話說明用途

    6. 團隊成員區塊：
       - 三個成員卡片（填入假名字）
       - 每個卡片有：大頭貼（用 CSS 做圓形彩色背景替代）、姓名、負責項目

    7. 頁尾（Footer）：
       - 「國立金門大學 工業工程與管理學系 2026 春季學期」
       - GitHub 連結圖示

    【設計風格】
    - 主色：深藍色（#1a237e）和靛藍色（#3949ab）
    - 強調色：亮橙色（#ff6f00）用於按鈕和重點
    - 字體：使用 Google Fonts 的 Noto Sans TC（支援中文）
    - 整體風格：科技感、專業、乾淨

    請直接輸出完整的 HTML 程式碼，不要有任何說明文字，不要有 markdown 代碼框。
````

5. 等待 AI 生成（約 30-60 秒）
6. 複製全部輸出的程式碼

<!-- 📸 截圖：AI Studio 生成完整 HTML 程式碼的畫面 -->

---

### 📌 任務 1-3：在本機預覽網頁

在上傳到 GitHub 之前，先在本機確認網頁正確：

1. 在電腦桌面新建一個資料夾，命名 `ie-portfolio`
2. 在資料夾裡新建一個文字檔案，命名 `index.html`
3. 用記事本（Windows）或 TextEdit（Mac）開啟 `index.html`
4. 把 AI 生成的程式碼全部貼進去，儲存
5. 用瀏覽器開啟 `index.html`（雙擊檔案，或把檔案拖到瀏覽器）
6. 確認網頁正確顯示，圖表有出現

<!-- 📸 截圖：瀏覽器開啟本機的 index.html，顯示完整網頁 -->

> [!NOTE]
> **如果圖表沒有出現：**
> Chart.js 需要網路連線才能從 CDN 載入。
> 用本機開啟時，確認電腦有連線到網路。
> 如果圖表還是沒有出現，可能是 AI 生成的 CDN 連結有誤，
> 手動改成：`https://cdn.jsdelivr.net/npm/chart.js`

---

### 📌 任務 1-4：上傳到 GitHub

**方法：直接在 GitHub 網頁上傳（不需要安裝 Git）**

1. 進入你的 `ie-portfolio` Repository
2. 點擊「**Add file**」→「**Upload files**」
3. 把 `index.html` 拖曳到上傳區域
4. 在「**Commit changes**」區域填寫：
    - Commit message：`feat: 新增首頁 HTML（AI 生成版）`
    - Extended description：`使用 Google AI Studio Gemini 2.0 Flash 生成，包含 Chart.js 互動圖表`
5. 點擊「**Commit changes**」

<!-- 📸 截圖：GitHub 上傳 index.html 的畫面，填寫 Commit message -->
<!-- 📸 截圖：Commit 完成，Repository 顯示 index.html 已上傳 -->

> [!NOTE]
> **Commit Message 的命名規範（業界慣例）：**
>
> | 前綴 | 意思 | 範例 |
> |------|------|------|
> | `feat:` | 新功能 | `feat: 新增導覽列` |
> | `fix:` | 修正錯誤 | `fix: 修正圖表顯示問題` |
> | `style:` | 樣式調整 | `style: 調整主色為深藍色` |
> | `docs:` | 文件更新 | `docs: 更新 README` |
> | `refactor:` | 重構（功能不變）| `refactor: 整理 CSS 結構` |
>
> 養成好的 Commit Message 習慣，讓你的 GitHub 頁面看起來專業。

> [!TIP]
> **🏆 第 1 小時 Checkpoint 完成！**
>
> - ✅ 建立 GitHub Repository，設定正確的基本資訊
> - ✅ 用 AI Studio 生成完整的單頁網站 HTML
> - ✅ 本機預覽確認網頁正確顯示
> - ✅ 上傳到 GitHub，第一個 Commit 完成

---

### 🎯 第 1 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 1-A】修改網頁內容（基礎）

不需要懂程式碼，直接修改 `index.html` 裡的文字：

1. 用記事本開啟 `index.html`
2. 用「尋找與取代」（Ctrl+H）把以下內容換成你自己的：
    - 假名字 → 你的真實姓名（或匿名）
    - 假統計數字 → 你認為合理的數字
    - 「工管系廠商實習處理中心」→ 你期末專題的名稱
3. 儲存後在瀏覽器重新整理，確認修改生效
4. 把修改後的檔案重新上傳 GitHub（第二個 Commit）

截圖修改前後的網頁對比。

---

#### 【練習 1-B】讓 AI 修改設計（進階）

發現網頁有什麼不滿意的地方，把你的需求告訴 AI Studio，讓它修改：

範例 Prompt：
````
以下是我的 HTML 網頁，請幫我做以下修改：
1. 把 Hero 區塊的背景改成從深紫色到深藍色的漸層
2. 在 Hero 按鈕旁邊加一個「GitHub 連結」按鈕（灰色，輪廓樣式）
3. 在頁面右下角加一個「回到頂部」的浮動按鈕

[貼上你的 HTML 程式碼]
````

把修改後的版本上傳 GitHub，截圖新的 Commit 記錄。

---

#### 【練習 1-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ 如果你用 GitHub 管理一個長期專案，
> 同時有三個同學在修改同一個 `index.html`，
> 會發生什麼問題？
> GitHub 的「Branch（分支）」和「Pull Request（合併請求）」
> 是用來解決什麼問題的？
> （請自行查詢這兩個概念並用自己的話解釋）

---

## ⏰ 第 2 小時：Vercel 部署 + 網站優化

> 🎯 **本小時目標：** 把 GitHub Repository 連結到 Vercel，
> 實現「Push 到 GitHub → 自動部署上線」的全自動發布流程，
> 並對網站進行功能和樣式的優化。

---

### 💡 觀念：Vercel 是什麼？為什麼用它？

**Vercel** 是一個專注於前端網站的雲端部署平台。
````
沒有 Vercel 的情況：
  寫好 index.html
  → 需要自己租伺服器（每月費用）
  → 需要設定伺服器環境（需要技術知識）
  → 需要手動上傳檔案到伺服器
  → 網域設定（還要另外付費）

有 Vercel 的情況：
  GitHub 有新 Commit
  → Vercel 自動偵測
  → 自動部署（約 30 秒）
  → 自動給你一個 https://xxx.vercel.app 網址
  → 完全免費（個人專案）
````

**Vercel vs GitHub Pages：**

| 比較 | Vercel | GitHub Pages |
|------|--------|-------------|
| 部署速度 | 約 30 秒 | 約 1-3 分鐘 |
| 網址格式 | `xxx.vercel.app` | `username.github.io/repo` |
| 自訂網域 | ✅ 免費支援 | ✅ 免費支援 |
| HTTPS | ✅ 自動 | ✅ 自動 |
| 適合類型 | 動態和靜態網站 | 靜態網站 |
| 免費限制 | 無限流量（個人）| 每月 1GB 流量 |

> **本課程選擇 Vercel** 的原因：
> 部署速度更快，且未來若要加入後端功能（API Routes）更方便。

---

### 📌 任務 2-1：連結 Vercel 和 GitHub

1. 前往 [vercel.com](https://vercel.com/)
2. 點擊「**Start Deploying**」
3. 選擇「**Continue with GitHub**」授權登入
4. 點擊「**Add New Project**」
5. 在 Repository 清單找到 `ie-portfolio`，點擊「**Import**」
6. 設定：

    | 設定項目 | 填入內容 |
    |---------|---------|
    | Project Name | `ie-portfolio`（自動填入）|
    | Framework Preset | **Other**（我們用純 HTML，不是框架）|
    | Root Directory | `./`（預設）|

7. 點擊「**Deploy**」
8. 等待約 30 秒，出現「🎉 Congratulations!」

<!-- 📸 截圖：Vercel 部署成功畫面，顯示網站預覽圖和網址 -->

---

### 📌 任務 2-2：確認自動部署流程

現在測試「Push 到 GitHub → Vercel 自動更新」的流程：

1. 回到 GitHub 的 `ie-portfolio` Repository
2. 點擊 `index.html` → 右上角「**✏️ 鉛筆圖示**」（直接在網頁上編輯）
3. 找到 Hero 區塊的副標題，改一個字（例如加一個「v2」）
4. 點擊「**Commit changes**」
5. 回到 Vercel Dashboard，觀察是否自動觸發新的部署
6. 部署完成後重新整理你的 `xxx.vercel.app` 網址，確認修改生效

<!-- 📸 截圖：Vercel Dashboard 顯示自動觸發的部署記錄 -->
<!-- 📸 截圖：Vercel 部署完成後，瀏覽器顯示更新後的網站 -->

> [!TIP]
> **這就是現代的 CI/CD（持續整合/持續部署）！**
> 你改一個字，系統自動幫你測試和部署，
> 不需要手動操作任何伺服器。
> 這是業界標準的工作流程。

---

### 📌 任務 2-3：新增第二個頁面 — 關於本課程

網站目前只有一個 `index.html`，
加入第二個頁面讓網站更完整：

**在 AI Studio 生成第二個頁面：**
````
請幫我生成一個 HTML 頁面（about.html），作為工管系課程介紹頁面。

技術要求：和 index.html 使用相同的設計風格（深藍色系，Noto Sans TC 字體）。
需要一個和 index.html 相同的導覽列，導覽列左側標題連結到 index.html。

頁面內容：
1. 頁面標題：「關於這門課」
2. 課程基本資訊卡片：
   - 課程名稱：雲端與數位內容管理
   - 學校：國立金門大學 工業工程與管理學系
   - 學期：2026 春季學期
   - 週數：14 週

3. 課程週次時間軸（Timeline）：
   用垂直時間軸的方式列出 14 週的學習重點：
   - Week 01：雲端大腦 + Make 自動化 + OCR AI 視覺
   - Week 02：智慧表單 + AutoCrat 文件工廠
   - Week 03：Make 進階 - Router、Iterator、錯誤處理
   - Week 04：Notion 架站
   - Week 05：API 深潛 - REST API × JSON
   - Week 06：資料庫設計 - 正規化與多表架構
   - Week 07：網頁爬蟲 - ParseHub × Make
   - Week 08：AI 文字分析 - Gemini × 情緒辨識
   - Week 09：Looker Studio 進階 × 視覺化工具比較
   - Week 10：全自動情報局整合
   - Week 11：GitHub + Vercel + AI 架站（本週！）
   - Week 12：Notion Database + Make 專案管理
   - Week 13：Slack 雲端協作整合
   - Week 14：期末整合專題發表

4. 技能雷達圖：
   用 Chart.js 做一個雷達圖，顯示學完這門課後的技能提升：
   - 自動化：5
   - AI 應用：4
   - 資料分析：4
   - 網頁開發：3
   - 資料庫設計：4
   - 雲端工具：5

請直接輸出完整 HTML，不要有說明文字，不要有 markdown 代碼框。
````

把生成的 `about.html` 上傳到 GitHub Repository。

<!-- 📸 截圖：about.html 成功部署，瀏覽器顯示課程介紹頁面和技能雷達圖 -->

---

### 📌 任務 2-4：修改導覽列連結

`index.html` 的導覽列需要加入連到 `about.html` 的連結：

1. 開啟 GitHub 上的 `index.html`，點擊鉛筆圖示編輯
2. 找到導覽列的 HTML（通常包含 `<nav>` 標籤）
3. 在導覽列加入：
````html
    <a href="about.html">關於本課</a>
````
4. Commit 修改，等待 Vercel 自動部署
5. 確認點擊「關於本課」可以正確跳到 `about.html`

<!-- 📸 截圖：兩個頁面可以互相跳轉的完整網站截圖 -->

> [!TIP]
> **🏆 第 2 小時 Checkpoint 完成！**
>
> - ✅ Vercel 連結 GitHub，自動部署流程設定完成
> - ✅ 測試 Push → 自動部署的 CI/CD 流程
> - ✅ 新增 about.html 第二個頁面（含技能雷達圖）
> - ✅ 兩個頁面之間的導覽連結正確設定

---

### 🎯 第 2 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人作業**

---

#### 【練習 2-A】新增 README.md（基礎）

GitHub Repository 的 README.md 是訪客看到的第一個頁面，
用 AI Studio 生成一份好看的 README：
````
請幫我生成一份 GitHub Repository 的 README.md，
用 Markdown 格式，內容包含：

1. 標題：工管系期末成果展示網站
2. Badges（徽章）：顯示 Vercel 部署狀態、GitHub 最後更新時間
3. 專案描述：說明這個網站是什麼、使用什麼工具建立的
4. 網站截圖區域（用文字佔位，之後手動加截圖）
5. 技術堆疊列表：HTML、CSS、JavaScript、Chart.js、AI 生成
6. 如何在本機執行（只需要瀏覽器開啟 index.html）
7. 課程資訊：國立金門大學工管系 2026 春季學期
````

把生成的內容貼到 GitHub 的 `README.md`（點擊編輯鉛筆圖示），
截圖 Repository 首頁顯示精美 README 的畫面。

---

#### 【練習 2-B】加入 Google Analytics（進階）

讓你的網站能追蹤訪客數量：

1. 前往 [Google Analytics](https://analytics.google.com/) 建立帳號
2. 建立「網站」屬性，網址填入你的 Vercel 網址
3. 取得追蹤碼（格式：`G-XXXXXXXXXX`）
4. 讓 AI Studio 幫你把追蹤碼加入 `index.html`：
````
    請在以下 HTML 的 <head> 區塊加入 Google Analytics 追蹤碼 G-XXXXXXXXXX：
    [貼上你的 HTML]
````
5. 上傳修改後的 `index.html` 到 GitHub

截圖 Google Analytics 即時報告，顯示有訪客（你自己）造訪網站。

---

#### 【練習 2-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ 這次你用 AI 生成的 HTML，
> 如果你不懂程式碼，未來維護這個網站會遇到什麼困難？
>
> 舉一個具體的例子：如果你想在「成果數據」區塊加入一個新的折線圖，
> 你會怎麼做？需要哪些步驟？
>
> 這說明了「Vibe Coding（用 AI 生成程式碼）」的什麼限制？
> 你認為工管系學生需要學到什麼程度的程式能力，
> 才能有效地使用 AI 輔助程式開發？

---

## ⏰ 第 3 小時：整合前幾週成果 + 期末專題網站規劃

> 🎯 **本小時目標：** 把 Week 01-10 學到的成果整合進網站，
> 並為期末專題建立專屬的展示頁面架構。

---

### 📌 任務 3-1：嵌入前幾週的成果

把以下三個外部服務嵌入到 `index.html` 的「成果展示」區塊：

---

**嵌入 Looker Studio 儀表板：**

1. 開啟 Week 09 建立的 Looker Studio 報表
2. 點擊右上角「**分享**」→「**嵌入報表**」
3. 複製嵌入連結（不是 iframe 代碼，而是 URL）
4. 讓 AI 幫你把嵌入連結加入 HTML：
````
    請在以下 HTML 的「成果展示」區塊的圖表後面，
    新增一個 Looker Studio 嵌入區塊：
    - 標題：「即時職缺分析儀表板」
    - 使用 iframe，src 填入：[你的 Looker Studio URL]
    - iframe 寬度：100%，高度：600px
    - 加上一個「開啟完整報表」的連結按鈕

    [貼上你的 HTML]
````

---

**嵌入 Notion 情報知識庫：**

1. 開啟 Week 10 建立的 Notion 情報知識庫頁面
2. 點擊「**Share**」→「**Publish**」確認已發布
3. 複製 Notion Site 的公開網址
4. 在 HTML 加入一個「前往情報知識庫」的按鈕，連結到 Notion

---

**新增系統架構圖：**

用 AI Studio 生成一個純 CSS/SVG 的系統架構圖，
不需要任何外部圖片：
````
請幫我用純 HTML/CSS 繪製一個系統架構圖，
放在我的 index.html 的「系統介紹」區塊。

架構圖內容（由左到右的流程）：
ParseHub → Make → Gemini AI → Notion + Sheets → Discord

每個節點用圓角矩形表示，用箭頭連接，
配色和網站整體風格一致（深藍色系）。
在行動裝置上改為垂直排列。

[貼上你的 HTML]
````

<!-- 📸 截圖：index.html 嵌入 Looker Studio 儀表板的畫面 -->
<!-- 📸 截圖：系統架構圖（純 CSS 製作）顯示完整的工具流程 -->

---

### 📌 任務 3-2：建立期末專題展示頁面模板

為期末專題（Week 14）建立一個 `project.html` 模板：
````
請幫我生成一個期末專題展示頁面（project.html）的 HTML 模板，
設計風格與 index.html 相同。

頁面結構：
1. 導覽列（與其他頁面一致）

2. 專題標題區塊：
   - 大標題：[專題名稱]（用方括號標記，表示之後填入）
   - 副標題：[一句話說明專題解決的問題]
   - 三個標籤：[主要工具1] [主要工具2] [主要工具3]

3. 問題與解法區塊（左右兩欄）：
   - 左欄：「原本的問題」（紅色邊框卡片）
   - 右欄：「我們的解法」（綠色邊框卡片）

4. 系統流程圖區塊：
   - 用 CSS Flexbox 排列 5-7 個流程節點
   - 每個節點可以填入工具名稱和圖示

5. 成果數據區塊：
   - 三個大數字展示（用 CSS 動畫讓數字從 0 增加到目標值）
   - 每個數字下方有說明文字

6. Demo 影片或截圖區塊：
   - 一個 16:9 的影片佔位框（灰色背景，文字「Demo 影片」）
   - 下方四個截圖格（2x2 網格）

7. 分工說明表格：
   - 表格欄位：組員、負責項目、完成度

8. 頁尾（與其他頁面一致）

所有方括號的地方都保留為可填入的空位，
不要填入具體內容，方便之後修改。

請直接輸出完整 HTML，不要有說明文字。
````

把 `project.html` 上傳到 GitHub，確認 Vercel 自動部署。

<!-- 📸 截圖：project.html 在瀏覽器的預覽畫面，顯示期末專題展示模板 -->

---

### 📌 任務 3-3：建立網站的完整 Sitemap

目前你的網站有三個頁面，更新導覽列讓它們串聯起來：

**網站結構：**
````
ie-portfolio（GitHub Repository）
    ├── index.html        → 首頁（成果總覽）
    ├── about.html        → 關於本課（課程介紹）
    ├── project.html      → 期末專題（Week 14 填入）
    └── README.md         → GitHub 說明文件
````

讓 AI 幫你更新三個頁面的導覽列，讓它們都有相同的四個選項：
````
首頁（index.html）| 關於本課（about.html）| 期末專題（project.html）| GitHub
````

**一次修改三個頁面並上傳，截圖三個 Commit 記錄，
確認導覽列在所有頁面都正確顯示。**

<!-- 📸 截圖：GitHub Repository 顯示所有檔案和清晰的 Commit 歷史記錄 -->

---

### 📌 任務 3-4：比較 Notion Sites vs GitHub + Vercel

實際用兩種工具各做了一個網站後，填寫以下比較表：

| 比較維度 | Notion Sites（Week 04）| GitHub + Vercel（Week 11）|
|---------|----------------------|--------------------------|
| 建立時間（第一次）| | |
| 更新內容的速度 | | |
| 樣式自由度（1-5分）| | |
| 互動功能豐富度 | | |
| 適合的對象 | | |
| 適合的情境 | | |
| 你的選擇是哪個？為什麼 | | |

> [!NOTE]
> **沒有絕對的好壞，只有適不適合：**
> - 快速發布說明文件 → Notion Sites
> - 需要完全自訂的作品集或展示網站 → GitHub + Vercel
> - 期末專題展示 → GitHub + Vercel（這週建立的）

> [!TIP]
> **🏆 第 3 小時 Checkpoint 完成！**
>
> - ✅ 把 Looker Studio 儀表板嵌入網站
> - ✅ 建立期末專題展示模板 project.html
> - ✅ 完成三頁面的導覽列串聯
> - ✅ 完成 Notion Sites vs GitHub + Vercel 的完整比較

---

### 🎯 第 3 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人 + 分組**

---

#### 【練習 3-A】自訂 Vercel 網域別名（個人，基礎）

Vercel 允許你把預設的 `xxx.vercel.app` 改成更好記的名稱：

1. 進入 Vercel Dashboard → 你的專案 → 「**Settings**」→「**Domains**」
2. 在「Add Domain」欄位輸入你想要的名稱，格式：`你的名字-ie.vercel.app`
3. 點擊「**Add**」
4. 確認新的網址可以正常開啟

截圖新的自訂網域名稱成功生效。

---

#### 【練習 3-B】整合挑戰（分組，2～3 人）

**為你們的期末專題建立完整的展示網站：**

基於本週建立的 `project.html` 模板，填入你們組的真實內容：

1. **首頁（index.html）**：改成你們組的專題總覽
2. **project.html**：填入以下真實內容：
    - 專題名稱和一句話說明
    - 問題（Before）和解法（After）的對比
    - 你們系統的實際流程圖（修改 CSS 節點內容）
    - 真實的成果數據（從你們的試算表取得）
    - 各組員的分工說明
3. 嵌入至少一個前幾週建立的成果：
    - Looker Studio 儀表板，或
    - Notion 情報知識庫，或
    - Make 劇本截圖圖表
4. 發布到 Vercel，取得公開網址

繳交：Vercel 公開網址（格式：`xxx.vercel.app`）
展示：Week 14 期末發表會使用這個網站進行報告

---

#### 【練習 3-C】延伸思考（挑戰）

> ❓ 這週你用 AI 生成了 HTML 網頁，
> 業界稱這種開發方式為「Vibe Coding」——
> 用自然語言描述需求，讓 AI 生成程式碼。
>
> 請思考以下三個問題：
>
> 1. **品質控制**：AI 生成的程式碼有時有安全漏洞或效能問題。
>    一個不懂程式的人，要怎麼知道 AI 生成的程式碼「夠好」？
>    有哪些工具或方法可以幫助非工程師評估程式碼品質？
>
> 2. **著作權問題**：AI 生成的程式碼的著作權屬於誰？
>    你、AI 公司（Google）、還是沒有著作權？
>    如果你把這個網站用於商業用途，有沒有法律風險？
>
> 3. **工程師的未來**：如果 AI 可以生成程式碼，
>    軟體工程師的工作會消失嗎？
>    工管系學生學會「用 AI 生成程式碼」這個技能，
>    在就業市場上的優勢和限制各是什麼？

---

## 🏆 本週總結

### 📊 本週技能地圖
````
【GitHub 基礎】
    ├── Repository：專案的程式碼倉庫
    ├── Commit：版本記錄（附說明訊息）
    ├── 在瀏覽器直接上傳和編輯（不需要安裝 Git）
    └── Commit Message 命名規範（feat/fix/style/docs）

        ↓ 程式碼有地方存了

【AI 生成網頁】
    ├── Google AI Studio：描述需求 → 生成完整 HTML/CSS/JS
    ├── 關鍵 Prompt 技巧：指定技術要求、頁面結構、設計風格
    ├── 迭代修改：把現有程式碼交給 AI 局部修改
    └── Chart.js CDN：在 AI 生成的頁面裡加入互動圖表

        ↓ 網站寫好了

【Vercel 自動部署】
    ├── 連結 GitHub Repository
    ├── Push → 自動偵測 → 30 秒部署完成
    ├── 取得 xxx.vercel.app 公開網址
    └── CI/CD 概念：程式改完自動上線

        ↓ 整合輸出

【成果整合】
    ├── 嵌入 Looker Studio 儀表板（iframe）
    ├── 連結 Notion 情報知識庫
    ├── 純 CSS 系統架構圖
    └── 三頁面網站（首頁/課程介紹/期末專題模板）
````

### 🔄 本週和前後週的連結
````
Week 04（Notion 架站）：快速靜態網站，無程式碼
    ↓ 本週升級
    → GitHub + Vercel：有版本控制的真實網站，AI 生成程式碼

Week 09（Looker Studio）：建立了職缺分析儀表板
    ↓ 本週整合
    → 嵌入到 GitHub 網站，作品集有真實的互動圖表

Week 14（期末專題）：需要一個展示平台
    ↓ 本週建立
    → project.html 模板已就緒，期末只需填入真實內容
````

### 🤔 課後思考問題

1. **版本控制的價值：** 你今天對 `index.html` 做了多次修改，
   每次都有 Commit 記錄。如果明天你發現今天的某個修改造成了問題，
   你要怎麼回到之前的版本？（提示：查詢 GitHub 的 Revert 功能）

2. **AI 生成程式碼的侷限：** 你的網站目前是完全靜態的——
   數字是寫死在 HTML 裡的，不會自動更新。
   如果要讓「本學期申請總數」這個數字自動從 Google Sheets 讀取，
   需要用什麼技術？（提示：查詢 Google Sheets API 和 JavaScript fetch）

3. **可及性（Accessibility）：** 你的網站對視覺障礙者友善嗎？
   查詢「Web Accessibility」的基本原則，
   列出你的網站可能違反的三個最常見問題，
   並說明如何用 AI 幫你修正。

### 📚 本週自學資源

| 資源 | 用途 |
|------|------|
| [GitHub 官方教學](https://docs.github.com/zh/get-started) | GitHub 完整操作說明 |
| [Vercel 文件](https://vercel.com/docs) | 部署設定和進階功能 |
| [Chart.js 文件](https://www.chartjs.org/docs/) | 所有圖表類型和設定選項 |
| [MDN Web Docs（中文）](https://developer.mozilla.org/zh-TW/) | HTML/CSS/JS 完整參考手冊 |
| [Can I use](https://caniuse.com/) | 查詢某個 CSS/JS 功能在各瀏覽器的支援度 |

---

### 📝 下週預告

> 🔜 **Week 12：專案管理自動化 — Notion Database 進階 × Make 整合**
>
> - Notion Database 進階：Relations（關聯）、Rollups（彙整）、Formula（公式）
> - 用 Notion Database 管理期末專題的進度（任務、截止日、負責人）
> - Make 整合：任務截止日前一天自動推播 Discord 提醒
> - 建立甘特圖視圖，視覺化期末專題時程
> - 把 GitHub Commit 記錄自動同步到 Notion 任務
>
> **課前任務：**
> 1. 確認你的期末專題題目已決定（Week 10 練習 3-C 的構想）
> 2. 把期末專題的所有待辦事項列出來（至少 10 項），下週建立 Notion 專案看板

---

*雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系 ｜ 2026 春季學期*
*Week 11 of 14*
