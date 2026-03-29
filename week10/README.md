# 🕵️ Week 10：全自動情報局 — 競品監控 × AI 摘要 × 推播整合

**雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系**

> **本週課程理念：** 前九週我們學了一堆工具，
> 但每個工具都是獨立運作的——爬蟲歸爬蟲、AI 歸 AI、視覺化歸視覺化。
>
> 真正有價值的系統，是讓所有工具協同運作，
> 讓「資料自動流入 → AI 自動判斷 → 人只看結果」。
>
> **今天我們把前九週學過的所有技術整合成一套完整的情報系統，
> 從資料採集到智慧推播，全程自動化，你只需要每天花五分鐘看通知。**

---

## 🎯 本週學習目標

學完本週，你將能夠：
- ✅ 設計一套「資料採集 → AI 分析 → 分級推播 → 知識庫累積」的完整自動化流水線
- ✅ 用 ParseHub 定時監控指定網站的新增內容
- ✅ 用 Gemini AI 自動摘要長文，產出「一句話重點」
- ✅ 用 Make Router 根據 AI 判斷的重要程度分流推播
- ✅ 用 Notion Database 建立自動累積的情報知識庫
- ✅ 整合 Looker Studio 即時顯示情報分布儀表板

---

## 🏢 本週實作情境：【工管系競爭情報局】

> 💼 **情境：** 系主任希望隨時掌握以下三類資訊，
> 但每天人工查找太花時間：
>
> 1. **產業動態**：台灣製造業、半導體、工業 4.0 的最新新聞
> 2. **政策公告**：教育部、勞動部和金門縣政府的相關公告
> 3. **就業市場**：工管相關職缺的新增和變化（Week 07 已建立）
>
> **今天的任務：**
> 建立一套「全自動情報局」，每天自動蒐集、摘要、分級、推播，
> 並把所有情報累積到 Notion 知識庫，方便日後查詢。

---

## ⏰ 第 1 小時：系統架構設計 + Notion 情報知識庫

> 🎯 **本小時目標：** 在動手建系統之前，
> 先把整個系統的架構設計清楚，並建立 Notion 知識庫作為情報的存放地點。

---

### 💡 觀念一：為什麼要先設計架構？

直接動手建系統，最常遇到的問題：
```
❌ 沒有設計架構的結果：
  → 爬蟲抓了資料，不知道存哪裡
  → AI 分析完了，不知道要觸發什麼
  → Discord 推播一堆訊息，找不到重要的
  → 三個月後試算表有幾千筆，沒有辦法查詢

✅ 先設計架構的結果：
  → 每個工具的輸入和輸出都清楚定義
  → 每個步驟都知道下一步是什麼
  → 系統出問題時知道從哪個節點開始除錯
```

---

### 💡 觀念二：完整系統架構圖
```
【資料採集層】
ParseHub 定時爬取
    ├── 產業新聞網站 → raw_news 試算表
    └── 政府公告網站 → raw_announcements 試算表

         ↓ Make Webhook 觸發

【AI 分析層】
Gemini 2.0 Flash
    ├── 摘要：把長文壓縮成一句話重點
    ├── 分類：產業動態 / 政策公告 / 其他
    ├── 重要程度：1（低）/ 2（中）/ 3（高）/ 4（緊急）
    └── 關鍵字：3-5 個標籤

         ↓ 結果回填試算表

【分流推播層】
Make Router（根據重要程度）
    ├── 重要程度 4（緊急）→ Discord #緊急情報 + 立即推播
    ├── 重要程度 3（高）  → Discord #重要情報
    ├── 重要程度 2（中）  → 每日彙整推播（晚上 18:00）
    └── 重要程度 1（低）  → 只存知識庫，不推播

         ↓ 同時執行

【知識庫層】
Notion Database
    ├── 每筆情報自動建立一則 Database 記錄
    ├── 欄位：標題、分類、重要程度、摘要、關鍵字、原始連結、日期
    └── 支援篩選、搜尋、關聯查詢

         ↓ 視覺化

【監控層】
Looker Studio 儀表板
    ├── 今日情報總數
    ├── 各分類分布
    ├── 重要程度分布
    └── 關鍵字趨勢
```

---

### 📌 任務 1-1：建立情報試算表

新建 Google Sheets，命名 `Week10_情報局`，建立以下工作表：

**工作表一：`raw_intel`（原始情報）**

| 欄位 | 說明 |
|------|------|
| intel_id | 情報編號（I001、I002...）|
| title | 標題 |
| content | 內文摘要或全文（前 500 字）|
| source_url | 原始連結 |
| source_name | 來源網站名稱 |
| crawl_date | 爬取日期 |
| ai_status | 待分析 / 已完成 / 分析失敗 |

**工作表二：`analyzed_intel`（分析後情報）**

| 欄位 | 說明 |
|------|------|
| intel_id | 對應 raw_intel 的編號（外鍵）|
| ai_summary | AI 摘要（一句話，50 字以內）|
| ai_category | AI 分類（產業動態/政策公告/就業市場/其他）|
| ai_importance | AI 重要程度（1-4 整數）|
| ai_keywords | AI 關鍵字（逗號分隔）|
| notion_page_id | 對應的 Notion Database 頁面 ID |
| push_status | 未推播 / 已推播 / 不推播 |

<!-- 📸 截圖：兩個工作表建立完成，顯示欄位結構 -->

---

### 📌 任務 1-2：建立 Notion 情報知識庫 Database

1. 在 Notion 新建一個頁面，命名：`🕵️ 工管系競爭情報局`
2. 輸入 `/database`，選擇「**Database - Full page**」
3. 命名：`情報知識庫`
4. 建立以下欄位：

    | 欄位名稱 | 類型 | 說明 |
    |---------|------|------|
    | 標題 | Title | 情報標題（主鍵欄位）|
    | 分類 | Select | 產業動態、政策公告、就業市場、其他 |
    | 重要程度 | Select | 🔴 緊急、🟠 高、🟡 中、⚪ 低 |
    | AI 摘要 | Text | Gemini 產生的一句話摘要 |
    | 關鍵字 | Multi-select | 自動標記的關鍵字 |
    | 來源網站 | Text | 原始資料來源 |
    | 原始連結 | URL | 可點擊的原始文章連結 |
    | 爬取日期 | Date | ParseHub 爬取的日期 |
    | 推播狀態 | Checkbox | 是否已推播 Discord |

5. 切換到 Gallery View，讓情報以卡片方式呈現
6. 新增 Board View，依「重要程度」分欄（可視化優先順序）

<!-- 📸 截圖：Notion Database 建立完成，顯示 Board View 依重要程度分欄 -->

---

### 📌 任務 1-3：在 Discord 建立情報局頻道群組

建立以下頻道，並各自建立 Webhook：

| 頻道名稱 | 用途 |
|---------|------|
| `#緊急情報` | 重要程度 4，立即推播 |
| `#重要情報` | 重要程度 3，即時推播 |
| `#每日彙整` | 重要程度 1-2，每天一次彙整 |
| `#系統狀態` | Make 劇本執行狀態和錯誤通知 |

把四個 Webhook URL 貼到記事本備用。

<!-- 📸 截圖：Discord 四個頻道建立完成 -->

> [!TIP]
> **🏆 第 1 小時 Checkpoint 完成！**
>
> - ✅ 設計完整的系統架構（四層：採集/分析/推播/知識庫）
> - ✅ 建立 Google Sheets 兩個工作表的欄位結構
> - ✅ 建立 Notion 情報知識庫 Database，支援多種視圖
> - ✅ 建立 Discord 四個情報頻道並取得 Webhook URL

---

### 🎯 第 1 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 1-A】Notion Database 視圖設計（基礎）

在情報知識庫 Database 新增兩個視圖：

1. **Calendar View**：依「爬取日期」顯示，讓你看出哪幾天情報最多
2. **Table View**：顯示所有欄位，並設定預設排序為「重要程度由高到低」

截圖兩個新視圖的畫面。

---

#### 【練習 1-B】架構圖繪製（進階）

用 Notion 的 `/mermaid` 或手繪後拍照，
把本小時設計的系統架構圖畫出來，放到 Notion 情報知識庫頁面。

架構圖必須包含：
- 每個工具的名稱（ParseHub、Make、Gemini、Notion、Discord、Looker Studio）
- 資料流向（箭頭）
- 觸發條件（定時/Webhook/手動）

---

#### 【練習 1-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ 這個「全自動情報局」系統如果要在真實企業環境部署，
> 你認為最大的風險是什麼？
> 請從以下三個角度各提出一個具體的風險和對應的緩解方案：
> 1. 技術風險（工具可能失效）
> 2. 資料品質風險（AI 摘要可能出錯）
> 3. 資訊過載風險（通知太多導致被忽略）

---

## ⏰ 第 2 小時：Make 整合劇本 — 採集到 AI 分析到知識庫

> 🎯 **本小時目標：** 建立 Make 的核心整合劇本，
> 把 ParseHub 爬回的資料自動送給 Gemini 分析，
> 分析結果同時寫入試算表和 Notion Database。

---

### 📌 任務 2-1：設定 ParseHub 監控目標

在 ParseHub 建立兩個爬蟲專案：

**專案 A：產業新聞（參考網站：工商時報製造業版面）**

欄位設定：
- `title`：新聞標題
- `summary`：新聞摘要或前兩段文字
- `article_url`：文章連結
- `publish_date`：發布日期

**專案 B：政府公告（參考網站：勞動部最新消息）**

欄位設定：
- `title`：公告標題
- `content`：公告內文（前 300 字）
- `announcement_url`：公告連結
- `announce_date`：公告日期

> [!WARNING]
> **爬取前請確認該網站允許爬蟲（檢查 robots.txt）。**
> 若目標網站不允許，請改用老師課堂指定的替代網站，
> 或使用各機關提供的 RSS Feed（通常完全合法）。

---

### 📌 任務 2-2：建立核心整合劇本

**劇本架構（共 10 個節點）：**
```
[Webhook 觸發]（由 ParseHub 完成時呼叫）
    ↓
[HTTP GET → 取得 ParseHub 執行結果]
    ↓
[JSON Parse → 解析爬蟲資料]
    ↓
[Iterator → 逐筆處理每則情報]
    ↓
[Google Sheets Add Row → 寫入 raw_intel]
    ↓
[HTTP POST → Gemini 分析（摘要+分類+重要程度+關鍵字）]
    ↓
[JSON Parse（兩層）→ 解析 AI 回傳 JSON]
    ↓
[Google Sheets Add Row → 寫入 analyzed_intel]
    ↓
[HTTP POST → 建立 Notion Database 頁面]
    ↓
[Router → 根據重要程度分流推播]
```

---

#### 節點一至三：取得爬蟲資料（同 Week 07）

參照 Week 07 第 3 小時的設定，
用 Webhook 接收 ParseHub 完成通知，再用 HTTP GET 取得結果。

---

#### 節點四：Iterator 逐筆處理

把爬蟲結果陣列用 Iterator 拆開，後續每個節點處理一筆情報。

---

#### 節點五：寫入 raw_intel

**Google Sheets → Add a Row：**

| 試算表欄位 | Make 變數 |
|---------|---------|
| intel_id | `I{{formatDate(now; "YYYYMMDDHHmmss")}}` |
| title | `{{value.title}}` |
| content | `{{substring(value.summary; 0; 500)}}` |
| source_url | `{{value.article_url}}` |
| source_name | `產業新聞`（固定文字）|
| crawl_date | `{{formatDate(now; "YYYY/MM/DD")}}` |
| ai_status | `待分析` |

> `{{substring(value.summary; 0; 500)}}` 取前 500 個字元，
> 避免超過試算表儲存格限制。

---

#### 節點六：Gemini AI 分析

**HTTP POST → Gemini API：**

URL：
```
https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=你的API_KEY
```

Body（JSON）：
```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "你是一位工業工程與管理系的情報分析師。\n請分析以下新聞或公告，以 JSON 格式回傳分析結果。\n\nJSON 格式（只回傳 JSON，不要有其他文字）：\n{\n  \"summary\": \"一句話重點（50字以內，用繁體中文）\",\n  \"category\": \"產業動態 或 政策公告 或 就業市場 或 其他\",\n  \"importance\": 整數1到4（1=低/2=中/3=高/4=緊急）,\n  \"keywords\": [\"關鍵字1\", \"關鍵字2\", \"關鍵字3\"]\n}\n\n重要程度判斷標準：\n- 4（緊急）：直接影響學生就業或系所運作的重大政策變化\n- 3（高）：與工管系密切相關的產業重要動態\n- 2（中）：一般產業資訊，有參考價值\n- 1（低）：邊緣相關或重複性資訊\n\n標題：{{title}}\n內容：{{content}}"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.1,
    "maxOutputTokens": 300
  }
}
```

---

#### 節點七：雙層 JSON Parse

第一層：解析 Gemini Response，取出 AI 說的文字：
```
{{candidates[]:content.parts[]:text}}
```

第二層：解析 AI 說的文字（JSON 字串 → 物件）

> [!WARNING]
> **防呆處理：** 在第二層 Parse 之前，先用 Tools → Set Variable 清除可能的代碼框：
> ```
> {{replace(replace(AItext; "```json\n"; ""); "\n```"; "")}}
> ```

---

#### 節點八：寫入 analyzed_intel

**Google Sheets → Add a Row：**

| 試算表欄位 | Make 變數 |
|---------|---------|
| intel_id | 從 raw_intel 回傳的 intel_id |
| ai_summary | `{{summary}}` |
| ai_category | `{{category}}` |
| ai_importance | `{{importance}}` |
| ai_keywords | `{{join(keywords[]; ", ")}}` |
| push_status | `未推播` |

---

#### 節點九：建立 Notion Database 頁面

這是本週最關鍵的新技術——用 Make 的 HTTP 模組呼叫 Notion API。

**步驟一：取得 Notion API Key**

1. 前往 [notion.so/my-integrations](https://www.notion.so/my-integrations)
2. 點擊「**New integration**」
3. 名稱：`情報局 Make 整合`
4. 權限：選擇你的情報知識庫所在的 Workspace
5. 複製「**Internal Integration Secret**」（格式：`secret_xxx...`）

**步驟二：把 Integration 加入 Database**

1. 開啟 Notion 情報知識庫 Database 頁面
2. 點擊右上角「**...**」→「**Connections**」→「**Add connections**」
3. 搜尋並選擇「情報局 Make 整合」

**步驟三：取得 Database ID**

從 Notion Database 的 URL 複製 Database ID：
```
https://www.notion.so/你的Workspace/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx?v=...
                                    ↑ 這 32 個字元就是 Database ID
```

**步驟四：在 Make 設定 HTTP POST 呼叫 Notion API**

1. 新增「**HTTP → Make a request**」
2. 設定：

    | 設定項目 | 填入內容 |
    |---------|---------|
    | URL | `https://api.notion.com/v1/pages` |
    | Method | `POST` |
    | Headers | `Authorization: Bearer secret_你的Key` |
    | Headers | `Notion-Version: 2022-06-28` |
    | Headers | `Content-Type: application/json` |
    | Body type | `Raw` |
    | Content type | `application/json` |

3. Body（JSON）：
```json
    {
      "parent": {
        "database_id": "你的Database_ID"
      },
      "properties": {
        "標題": {
          "title": [
            {
              "text": {
                "content": "{{title}}"
              }
            }
          ]
        },
        "分類": {
          "select": {
            "name": "{{category}}"
          }
        },
        "重要程度": {
          "select": {
            "name": "{{importanceLabel}}"
          }
        },
        "AI 摘要": {
          "rich_text": [
            {
              "text": {
                "content": "{{summary}}"
              }
            }
          ]
        },
        "來源網站": {
          "rich_text": [
            {
              "text": {
                "content": "{{source_name}}"
              }
            }
          ]
        },
        "原始連結": {
          "url": "{{source_url}}"
        },
        "爬取日期": {
          "date": {
            "start": "{{formatDate(now; \"YYYY-MM-DD\")}}"
          }
        }
      }
    }
```

> [!NOTE]
> **`importanceLabel` 是什麼？**
> Notion 的 Select 欄位需要文字，但 AI 回傳的是數字（1-4）。
> 在這個節點之前，先用 **Tools → Set Variable** 轉換：
>
> ```
> 欄位名稱：importanceLabel
> 值：
> {{if(importance = 4; "🔴 緊急";
>    if(importance = 3; "🟠 高";
>    if(importance = 2; "🟡 中";
>    "⚪ 低")))}}
> ```

<!-- 📸 截圖：Notion API 呼叫設定的 Headers 和 Body -->
<!-- 📸 截圖：Notion 情報知識庫 Database 新增了一筆 Make 自動建立的情報 -->

---

#### 節點十：Router 分流推播

**Router 設定四條支線：**

**支線 1 — 緊急（importance = 4）：**
```
🚨 **【緊急情報】**

▸ **標題：** {{title}}
▸ **分類：** {{category}}
▸ **AI 摘要：** {{summary}}
▸ **關鍵字：** {{ai_keywords}}
▸ **來源：** {{source_name}}
▸ **連結：** {{source_url}}
▸ **爬取時間：** {{formatDate(now; "MM/DD HH:mm")}}

⚠️ 請相關人員立即查閱原文。
```
→ 推播到 `#緊急情報`

**支線 2 — 高重要性（importance = 3）：**
```
📌 **【重要情報】**

▸ **標題：** {{title}}
▸ **分類：** {{category}}
▸ **AI 摘要：** {{summary}}
▸ **來源：** {{source_name}} | {{formatDate(now; "MM/DD")}}
```
→ 推播到 `#重要情報`

**支線 3 — 中重要性（importance = 2）：**
- 不立即推播
- 更新 `analyzed_intel` 的 `push_status` 為 `待彙整`（等待每日彙整劇本）

**支線 4 — 低重要性（importance = 1）：**
- 更新 `analyzed_intel` 的 `push_status` 為 `不推播`
- 情報只存在 Notion 知識庫，不進入 Discord

<!-- 📸 截圖：Make 完整劇本畫布，顯示十個節點和 Router 四條支線 -->

---

### 📌 任務 2-3：手動測試整合劇本

1. 在 `raw_intel` 手動填入 3 筆測試情報（不同重要程度）
2. 點擊「**Run once**」
3. 確認：
    - `analyzed_intel` 有對應的 AI 分析結果
    - Notion Database 新增了對應的情報頁面
    - Discord `#緊急情報` 或 `#重要情報` 收到通知

<!-- 📸 截圖：Make 執行完成，顯示每個節點處理的資料 -->

> [!TIP]
> **🏆 第 2 小時 Checkpoint 完成！**
>
> - ✅ 設定 ParseHub 兩個爬蟲專案
> - ✅ 建立 10 節點的核心整合劇本
> - ✅ 成功呼叫 Notion API，自動在 Database 建立頁面
> - ✅ Router 四條支線根據重要程度分流推播

---

### 🎯 第 2 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人作業**

---

#### 【練習 2-A】Notion API 欄位擴充（基礎）

修改 Notion API 的 Body，新增「關鍵字」欄位的寫入：

Notion 的 Multi-select 欄位格式如下：
```json
"關鍵字": {
  "multi_select": [
    {"name": "關鍵字1"},
    {"name": "關鍵字2"},
    {"name": "關鍵字3"}
  ]
}
```

> [!TIP]
> AI 回傳的 `keywords` 是一個陣列，
> 你需要用 Make 的 `array` 函數把它轉換成 Notion 需要的格式。
> 可以考慮用三個獨立的 `{"name": "{{keywords[1]}}"}` 分別填入。

截圖 Notion Database 中新增的情報，確認關鍵字欄位正確顯示。

---

#### 【練習 2-B】錯誤處理完整版（進階）

為這個十節點劇本的以下三個關鍵節點加入錯誤處理：

1. **Gemini API 節點**：AI 分析失敗時
   - 更新 `raw_intel` 的 `ai_status` 為 `分析失敗`
   - 推播 `#系統狀態` 頻道通知

2. **Notion API 節點**：Database 頁面建立失敗時
   - 把 Notion 建立失敗的情報記錄到試算表的 `error_log` 工作表
   - 推播 `#系統狀態` 通知

3. **整體劇本失敗時**：Make 提供「Incomplete executions」功能，
   設定失敗的執行保存 30 天，方便手動重試

截圖加入所有錯誤處理後的完整劇本畫布。

---

#### 【練習 2-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ 這個系統用 Notion API 自動建立頁面，
> 但 Notion API 有速率限制（每秒 3 次請求）。
>
> 如果 ParseHub 一次爬回 50 筆新聞，Iterator 會快速連續呼叫 Notion API 50 次，
> 很可能觸發速率限制導致部分頁面建立失敗。
>
> 你有哪些方法可以解決這個問題？
> 請提出至少兩種方案，並說明各自的優缺點。

---

## ⏰ 第 3 小時：每日彙整推播 + Looker Studio 情報儀表板

> 🎯 **本小時目標：** 建立「每日彙整」劇本，
> 把中低重要性情報整理成每日摘要推播，
> 並建立即時的情報分布儀表板。

---

### 📌 任務 3-1：建立每日彙整劇本

中重要性的情報（importance = 2）不需要即時推播，
但也不應該被忽略，每天整理一次推播給相關人員。

**劇本架構：**
```
[Schedule 每天 18:00]
    ↓
[Google Sheets Search Rows]（搜尋 push_status = "待彙整"）
    ↓
[Aggregator → 把所有情報彙整成一個文字塊]
    ↓
[HTTP POST → Gemini 生成每日摘要]
    ↓
[Discord 推播到 #每日彙整]
    ↓
[Google Sheets Update → 把 push_status 改為 "已推播"]
```

---

#### 節點一：Schedule 觸發器

設定每天下午 6 點執行一次。

---

#### 節點二：Google Sheets Search Rows

搜尋所有 `push_status = 待彙整` 的情報：

| 設定 | 值 |
|------|---|
| Spreadsheet | `Week10_情報局` |
| Sheet | `analyzed_intel` |
| Filter | `push_status` Equal to `待彙整` |
| Limit | `20`（每次最多彙整 20 筆）|

---

#### 節點三：Text Aggregator 彙整文字

1. 新增「**Text Aggregator**」
2. Source Module：選擇 Search Rows 節點
3. Text template：
```
    [{{ai_category}}] {{ai_summary}}（來源：{{source_name}}）
```

Aggregator 會把所有符合條件的情報，
合併成一段多行文字，方便送給 AI 做二次摘要。

---

#### 節點四：Gemini 生成每日摘要

把 Aggregator 彙整的情報清單送給 Gemini，
請它生成一份「每日情報簡報」：
```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "你是一位工業工程與管理系的情報分析師。\n以下是今天蒐集到的各項情報摘要，請整理成一份簡潔的每日情報簡報。\n\n格式要求：\n1. 開頭寫今天的日期\n2. 依分類分組（產業動態、政策公告等）\n3. 每則情報用一行表示\n4. 結尾寫一句「今日重點觀察」（30字以內）\n5. 全文用繁體中文，不超過 500 字\n\n今日情報清單：\n{{aggregated_text}}"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.3,
    "maxOutputTokens": 800
  }
}
```

> [!NOTE]
> 這裡的 `temperature` 設定比較高（0.3），
> 因為「生成簡報」比「分類」更需要一點文字創意，
> 但還是保持在低溫區間，確保內容準確。

---

#### 節點五：Discord 推播每日彙整
```
📰 **【每日情報彙整】{{formatDate(now; "YYYY/MM/DD")}}**

{{ai_daily_summary}}

---
📊 今日統計：
▸ 彙整情報數：{{total_count}} 則
▸ 詳細情報請查閱 Notion 知識庫

🔗 情報知識庫：https://你的Notion頁面連結
```

→ 推播到 `#每日彙整` 頻道

---

#### 節點六：批次更新推播狀態

1. 新增「**Google Sheets → Update Rows**」
2. 把所有已彙整的情報的 `push_status` 更新為 `已推播`

<!-- 📸 截圖：每日彙整劇本的完整畫布（六個節點）-->
<!-- 📸 截圖：Discord #每日彙整 頻道收到 AI 生成的每日情報簡報 -->

---

### 📌 任務 3-2：建立 Looker Studio 情報儀表板

新建 Looker Studio 報表，連結 `analyzed_intel` 工作表：

**記分卡區（四個）：**

| 指標 | 計算方式 |
|------|---------|
| 今日新增情報數 | COUNTIF crawl_date = TODAY() |
| 緊急情報數 | COUNTIF ai_importance = 4 |
| 待彙整情報數 | COUNTIF push_status = "待彙整" |
| 本週情報總數 | COUNTIF 過去 7 天 |

**圖表一：重要程度分布（圓餅圖）**
- 維度：`ai_importance`
- 指標：計數

**圖表二：分類分布趨勢（折線圖）**
- 維度：`crawl_date`（按天）
- 分解維度：`ai_category`
- 指標：計數

**圖表三：來源網站情報量（水平長條圖）**
- 維度：`source_name`
- 指標：計數

**表格：最新緊急和高重要情報**
- 篩選：`ai_importance >= 3`
- 排序：`crawl_date` 降序（最新的在最上面）
- 欄位：`title`、`ai_category`、`ai_summary`、`ai_importance`、`crawl_date`

<!-- 📸 截圖：情報局 Looker Studio 儀表板完整畫面 -->

---

### 📌 任務 3-3：把儀表板嵌入 Notion

1. 在 Looker Studio 點擊「**分享**」→「**嵌入報表**」→ 複製連結
2. 在 Notion 的「🕵️ 工管系競爭情報局」頁面，
   在 Database 上方新增一個 `/embed` Block，嵌入 Looker Studio 儀表板
3. 讓情報知識庫頁面同時顯示：
    - 上方：即時儀表板（Looker Studio）
    - 下方：完整情報列表（Notion Database）

<!-- 📸 截圖：Notion 情報知識庫頁面，上方是 Looker Studio 儀表板，下方是 Database -->

---

### 📌 任務 3-4：完整系統壓力測試

模擬完整的一個工作週期：

1. **手動觸發 ParseHub** 爬取一次資料
2. **劇本 A 自動執行**：資料寫入試算表 → AI 分析 → Notion 建立頁面 → 立即推播
3. **等待到傍晚或手動觸發每日彙整劇本**
4. 確認 `#每日彙整` 收到 AI 生成的每日簡報
5. 確認 Notion Database 有完整的情報記錄
6. 確認 Looker Studio 儀表板數字正確

<!-- 📸 截圖：壓力測試完成，Notion Database 顯示多筆情報記錄，標籤和重要程度正確 -->

> [!TIP]
> **🏆 第 3 小時 Checkpoint 完成！**
>
> - ✅ 建立每日彙整劇本（Text Aggregator + Gemini 二次摘要 + Discord 推播）
> - ✅ 建立情報分布 Looker Studio 儀表板（記分卡 + 四種圖表）
> - ✅ 把儀表板嵌入 Notion 情報知識庫頁面
> - ✅ 完成完整系統的壓力測試

---

### 🎯 第 3 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人 + 分組**

---

#### 【練習 3-A】個人化情報訂閱（個人，基礎）

修改每日彙整劇本，讓不同同學可以訂閱不同分類的情報：

在 `analyzed_intel` 試算表新增一欄 `target_audience`，
根據 `ai_category` 自動填入（用公式）：
```
=IF(ai_category="產業動態", "系主任,產業組",
 IF(ai_category="政策公告", "系主任,行政組",
 IF(ai_category="就業市場", "全體學生", "系主任")))
```

修改每日彙整劇本，讓它根據 `target_audience` 推播到不同的 Discord 頻道。

---

#### 【練習 3-B】整合挑戰（分組，2～3 人）

**打造你們小組的「自訂情報局」：**

選擇一個你們小組關心的主題（不限工管，例如：
金門縣觀光動態、台灣半導體產業、某個你有興趣的企業動向），
建立一套完整的情報系統：

1. **ParseHub** 爬取相關主題的網站
2. **Gemini** 分析摘要，針對你們的主題客製化 Prompt
3. **Make** 四路分流，根據重要程度推播
4. **Notion** 情報知識庫，累積所有情報
5. **Looker Studio** 即時儀表板，呈現情報分布

繳交：
- 系統架構圖（用 Notion mermaid 或手繪）
- Make 劇本截圖
- Notion 情報知識庫截圖（至少 10 筆情報）
- Discord 推播截圖
- Looker Studio 儀表板截圖

---

#### 【練習 3-C】期末專題預備（思考題，重要）

> ❓ **這是期末專題的前置思考，請認真作答。**
>
> Week 14 你需要展示一個「整合性專題」，
> 把本學期學到的工具串成一套有實際應用價值的系統。
>
> 請現在就開始規劃：
>
> 1. **你選擇的情境是什麼？**
>    （工管系的行政流程？某個產業的監控需求？你個人感興趣的問題？）
>
> 2. **你的系統用到哪些工具？**
>    （至少用到 Week 01-10 學過的 5 個以上工具）
>
> 3. **系統的資料流是什麼？**
>    （從哪裡進來，經過哪些處理，最後輸出什麼）
>
> 4. **你的系統解決了什麼問題？**
>    （如果沒有這套系統，人工要花多少時間？）
>
> 把你的構想（200 字以內）貼到課堂共用文件，
> 老師下週會給初步回饋。

---

## 🏆 本週總結

### 📊 本週技能地圖
```
【系統設計層】
    ├── 四層架構：採集/分析/推播/知識庫
    ├── 資料流設計：每個節點的輸入和輸出
    └── 容錯設計：錯誤處理和重試機制

【工具整合層】
    ├── ParseHub API → Make Webhook
    ├── Make → Gemini（摘要+分類+重要程度+關鍵字）
    ├── Make → Notion API（自動建立 Database 頁面）
    └── Make Router → Discord 四頻道分流

【彙整輸出層】
    ├── Text Aggregator：把多筆資料合成一段文字
    ├── Gemini 二次摘要：生成每日情報簡報
    └── Schedule 每日推播：每天 18:00 自動執行

【視覺化層】
    ├── Looker Studio：即時情報分布儀表板
    └── Notion：情報知識庫 + 嵌入儀表板
```

### 🔄 本週使用到哪些前幾週的技術

| 技術 | 首次學習 | 本週應用方式 |
|------|---------|-----------|
| Make HTTP 模組 | Week 01 | 呼叫 Notion API、Gemini API |
| Make Router | Week 03 | 根據重要程度四路分流 |
| Make Iterator | Week 03 | 逐筆處理每則情報 |
| Gemini API | Week 08 | 摘要+分類+重要程度+每日簡報 |
| ParseHub API | Week 07 | 自動採集新聞和公告 |
| Notion Database | Week 04 | 情報知識庫 |
| Looker Studio | Week 09 | 情報分布儀表板 |
| 資料清洗公式 | Week 06 | 處理爬蟲骯髒資料 |

### 🤔 課後思考問題

1. **資訊過載的設計哲學：** 你的系統每天可能推播幾十則情報，
   長期下來會讓人產生「通知疲勞」，開始忽略所有通知。
   你認為一個「好的」情報系統，每天最多應該推播幾則訊息？
   如何設計讓系統自動控制推播量？

2. **AI 摘要的可信度：** Gemini 的摘要是它「理解後重述」的結果，
   可能遺漏重要細節，甚至產生輕微的事實偏差。
   在一個用於決策的情報系統中，你如何設計「人機協作」的工作流，
   讓 AI 負責苦力工作，人類負責關鍵判斷？

3. **系統的邊界：** 這個系統的功能越強，對目標網站的爬取量也越大。
   如果你要把這個系統交給不懂技術的行政人員使用，
   你需要加入哪些「護欄（guardrails）」，
   確保他們不會在不知情的情況下違反爬蟲倫理？

### 📚 本週自學資源

| 資源 | 用途 |
|------|------|
| [Notion API 官方文件](https://developers.notion.com/reference/intro) | Notion API 完整端點說明 |
| [Notion API Database 操作](https://developers.notion.com/reference/post-page) | 建立頁面的 API 格式 |
| [Make Text Aggregator 說明](https://www.make.com/en/help/tools/text-aggregator) | Aggregator 使用方式 |
| [Gemini API Prompt 最佳實踐](https://ai.google.dev/docs/prompt_best_practices) | 提升 AI 輸出品質 |

---

### 📝 下週預告

> 🔜 **Week 11：AI 架站 — GitHub + Vercel + AI 生成互動網頁**
>
> - GitHub 基礎：Repository、Commit、Push 的操作流程
> - 用 Google AI Studio（Gemini）直接描述需求，生成 HTML/CSS/JS 網頁
> - Vercel 一鍵部署：從 GitHub 到公開網址只需 3 分鐘
> - 把期末專題的成果頁面做出來（有互動圖表、動態資料的網站）
> - Week 04 Notion 架站（簡單版）vs Week 11 GitHub 架站（進階版）完整比較
>
> **課前任務：**
> 1. 建立 [GitHub](https://github.com/) 帳號（免費）
> 2. 建立 [Vercel](https://vercel.com/) 帳號，用 GitHub 帳號登入
> 3. 在 GitHub 建立你的第一個 Repository，命名為 `my-portfolio`
> 4. 確認你的 Google AI Studio 帳號可以正常登入

---

*雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系 ｜ 2026 春季學期*
*Week 10 of 14*
