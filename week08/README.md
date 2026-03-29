# 🤖 Week 08：AI 文字分析 — Gemini × Make 情緒辨識與自動分類

**雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系**

> **本週課程理念：** 上週我們蒐集了大量職缺資料，
> 但一百筆職缺，你要怎麼快速判斷哪些適合你？
> 人工一筆一筆看要花幾個小時，AI 幫你分析只需要幾秒鐘。
>
> **今天我們把 AI 的「大腦」接進工作流，
> 讓它自動讀懂文字、分類資料、做出判斷——
> 你只需要告訴它「怎麼判斷」。**

---

## 🎯 本週學習目標

學完本週，你將能夠：
- ✅ 理解 Prompt Engineering 的核心原則，知道怎麼問 AI 才能得到結構化回答
- ✅ 申請並使用 Gemini API（`gemini-2.0-flash`，完全免費）
- ✅ 在 Make 裡串接 Gemini API，對文字內容自動分類和分析
- ✅ 用 Make Router 根據 AI 分類結果分流處理
- ✅ 建立「AI 輔助職缺篩選系統」，自動標記適合工管系學生的職缺

---

## 🏢 本週實作情境：【工管系職缺 AI 篩選系統】

> 💼 **情境延續：** Week 07 建立的職缺情報站每週自動蒐集資料，
> 但系辦收到學生反映：「職缺太多了，不知道哪些適合自己。」
>
> **今天的任務：** 讓 AI 自動讀取每筆職缺的工作描述，
> 分析以下三件事：
> 1. **技能等級分類**：入門 / 中階 / 資深（根據要求年資和技能）
> 2. **工管相關性評分**：1-5 分（這個職缺和工管系所學的相關程度）
> 3. **關鍵技能標記**：抽取職缺要求的三到五個核心技能

---

## ⏰ 第 1 小時：Prompt Engineering — 怎麼和 AI 說話

> 🎯 **本小時目標：** 理解 Prompt Engineering 的核心技巧，
> 能夠設計出讓 AI 回傳**結構化 JSON 格式**的 Prompt。

---

### 💡 觀念一：什麼是 Prompt Engineering？

**Prompt Engineering（提示工程）** 是設計「輸入給 AI 的指令」的技術。

同樣的問題，不同的問法得到完全不同的結果：
```
❌ 爛的 Prompt：
「幫我分析這個職缺」

✅ 好的 Prompt：
「你是一位工業工程系的就業輔導老師。
請分析以下職缺描述，並以 JSON 格式回傳以下資訊：
1. level：入門/中階/資深（依照要求年資判斷）
2. ie_score：1到5的整數（和工業工程系的相關程度）
3. skills：陣列，列出最重要的3到5個技能關鍵字
4. reason：一句話說明分類理由

只回傳 JSON，不要有其他文字。
職缺描述如下：{job_description}」
```

---

### 💡 觀念二：好 Prompt 的五個要素

| 要素 | 說明 | 範例 |
|------|------|------|
| **角色設定** | 告訴 AI 它扮演什麼角色 | 「你是工業工程系的就業輔導老師」 |
| **任務說明** | 清楚說明要做什麼 | 「分析職缺並分類技能等級」 |
| **輸出格式** | 指定回傳的格式 | 「以 JSON 格式回傳，欄位為...」 |
| **限制條件** | 明確的邊界 | 「只回傳 JSON，不要有其他文字」 |
| **輸入資料** | 要分析的內容 | `{job_description}` 動態帶入 |

---

### 💡 觀念三：為什麼要讓 AI 回傳 JSON？

如果 AI 回傳自然語言：
```
「這個職缺是中階職位，需要約3年經驗，
 和工管系的相關性大約是4分，
 主要需要的技能是精實生產、品質管理和數據分析。」
```

你沒辦法用程式自動處理這段文字——
哪個字是等級？哪個是分數？怎麼切出技能清單？

如果 AI 回傳 JSON：
```json
{
  "level": "中階",
  "ie_score": 4,
  "skills": ["精實生產", "品質管理", "數據分析"],
  "reason": "要求3年以上品質管理經驗，與工管核心課程高度相關"
}
```

Make 的 JSON Parse 模組直接解析，每個欄位都能獨立使用。

> [!NOTE]
> **這是 AI 自動化的關鍵設計原則：**
> AI 的輸出必須是機器可讀的格式，才能被後續的自動化流程使用。
> JSON 是最適合的選擇。

---

### 📌 任務 1-1：申請 Gemini API Key

1. 前往 [Google AI Studio](https://aistudio.google.com/)
2. 用 Google 帳號登入
3. 點擊左側「**Get API key**」→「**Create API key**」
4. 選擇一個 Google Cloud 專案（或建立新專案）
5. 複製 API Key（格式：`AIzaSy...`），貼到記事本備用

> [!WARNING]
> **重要：本課程固定使用 `gemini-2.0-flash` 模型。**
> 這個模型有免費的 API 額度，不需要信用卡。
> 不要使用 `gemini-1.5-pro` 或其他模型，免費額度可能為零。

<!-- 📸 截圖：Google AI Studio 取得 API Key 的畫面（Key 遮住後半段）-->

---

### 📌 任務 1-2：在 Google AI Studio 測試 Prompt

在搬到 Make 之前，先在 AI Studio 的 Playground 調整 Prompt：

1. 點擊左側「**Stream realtime**」或「**Create a prompt**」
2. 在右側 Model 選擇「**Gemini 2.0 Flash**」
3. 在 System instruction 區域輸入：
```
    你是一位工業工程與管理系的就業輔導專家。
    你的任務是分析職缺描述，評估該職缺對工管系學生的適合程度。
    你必須嚴格按照要求的 JSON 格式回答，不得輸出任何其他文字。
```

4. 在對話框輸入以下測試 Prompt：
```
    請分析以下職缺描述，並以 JSON 格式回傳分析結果。

    JSON 格式要求（嚴格遵守）：
    {
      "level": "入門 或 中階 或 資深",
      "ie_score": 1到5的整數,
      "skills": ["技能1", "技能2", "技能3"],
      "reason": "一句話說明分類理由（30字以內）"
    }

    只回傳 JSON，不要有 markdown 代碼框（不要有 ```json），
    不要有任何說明文字。

    職缺描述：
    「誠徵工業工程師，需具備2年以上製程改善經驗，
     熟悉精實生產（Lean Manufacturing）和六標準差（6 Sigma）。
     負責生產線平衡、瓶頸分析及工時研究。
     具備 Minitab 統計分析軟體使用經驗者佳。」
```

5. 點擊送出，確認 AI 回傳純 JSON 格式

<!-- 📸 截圖：Google AI Studio 測試 Prompt，AI 回傳 JSON 格式的結果 -->

---

### 📌 任務 1-3：Prompt 調整實驗

測試以下三個版本的 Prompt，觀察 AI 回傳的差異：

**版本 A（指令不夠明確）：**
```
分析這個職缺是幾年經驗的：「需2年以上製程改善經驗...」
```

**版本 B（指令清楚但沒有格式限制）：**
```
分析以下職缺的技能等級（入門/中階/資深）和工管相關性（1-5分）：
「需2年以上製程改善經驗...」
```

**版本 C（完整的 Prompt Engineering）：**
（使用任務 1-2 的完整版本）

把三個版本的 AI 回應截圖，填寫以下比較表：

| 版本 | AI 回應格式 | 可以直接用程式解析嗎？ | 問題 |
|------|-----------|-------------------|------|
| A | | | |
| B | | | |
| C | | | |

> [!TIP]
> **🏆 第 1 小時 Checkpoint 完成！**
>
> - ✅ 取得 Gemini API Key
> - ✅ 理解 Prompt Engineering 的五個要素
> - ✅ 在 AI Studio 成功設計出回傳 JSON 的 Prompt
> - ✅ 透過三版本比較，體驗 Prompt 品質的差異

---

### 🎯 第 1 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 1-A】Prompt 改版（基礎）

修改任務 1-2 的 Prompt，讓 AI 額外回傳一個欄位：
```json
"suitable_year": "適合幾年級的學生（大二/大三/大四/研究生）"
```

在 AI Studio 測試修改後的 Prompt，截圖 AI 回傳包含新欄位的 JSON。

---

#### 【練習 1-B】邊界測試（進階）

測試以下兩種「刁難」情況，看 AI 如何處理，並說明你的觀察：

**情況一：職缺描述非常短**
```
「誠徵工程師一名，有興趣者請投履歷。」
```

**情況二：職缺描述不是工管相關**
```
「誠徵廚師，需有五年中餐料理經驗，
 擅長廣東料理，能獨立管理廚房作業。」
```

思考並回答：當 AI 遇到無法判斷的情況，你的 Prompt 應該怎麼設計「防呆機制」，確保它還是回傳合法的 JSON？

---

#### 【練習 1-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ Prompt Engineering 和傳統的「寫程式」相比，
> 有哪些優點和缺點？
> 在什麼情況下你會選擇用 AI + Prompt，
> 什麼情況下你會選擇直接寫程式（例如用 GAS 的 if / else）？

---

## ⏰ 第 2 小時：在 Make 串接 Gemini API

> 🎯 **本小時目標：** 把 Gemini API 接入 Make，
> 讓試算表裡的每筆職缺資料自動送給 AI 分析，
> 結果自動回填到試算表。

---

### 💡 觀念：Gemini API 的請求結構

先在 Hoppscotch 確認 Gemini API 的格式，再搬到 Make：

**端點：**
```
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=你的API_KEY
```

**Request Body（JSON 格式）：**
```json
{
  "contents": [
    {
      "parts": [
        {
          "text": "你的 Prompt 內容"
        }
      ]
    }
  ],
  "generationConfig": {
    "temperature": 0.1,
    "maxOutputTokens": 500
  }
}
```

**Response 結構：**
```json
{
  "candidates": [
    {
      "content": {
        "parts": [
          {
            "text": "{\"level\": \"中階\", \"ie_score\": 4, ...}"
          }
        ]
      }
    }
  ]
}
```

> [!NOTE]
> **temperature 設定：**
> `temperature` 控制 AI 回答的「隨機程度」，範圍 0 到 1。
> - `0`：最確定，每次回答幾乎一樣（適合分類任務）
> - `1`：最隨機，每次回答可能不同（適合創意寫作）
>
> 分類任務請設定 `0.1`，確保結果一致。

---

### 📌 任務 2-1：在 Hoppscotch 測試 Gemini API

1. 方法：**POST**
2. URL：
```
    https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=你的API_KEY
```
3. Headers 新增：

    | Key | Value |
    |-----|-------|
    | `Content-Type` | `application/json` |

4. Body 選 `raw` → `JSON`，輸入：
```json
    {
      "contents": [
        {
          "parts": [
            {
              "text": "請分析以下職缺描述，以 JSON 格式回傳：{\"level\": \"入門或中階或資深\", \"ie_score\": 整數1到5, \"skills\": [\"技能1\",\"技能2\",\"技能3\"], \"reason\": \"一句話說明\"}。只回傳 JSON，不要有其他文字。職缺描述：誠徵工業工程師，需2年以上製程改善經驗，熟悉精實生產和六標準差。"
            }
          ]
        }
      ],
      "generationConfig": {
        "temperature": 0.1,
        "maxOutputTokens": 500
      }
    }
```

5. 點擊「**Send**」，確認 Response 包含 AI 分析結果

<!-- 📸 截圖：Hoppscotch 成功呼叫 Gemini API，回傳 JSON 格式的分析結果 -->

---

### 📌 任務 2-2：準備試算表

在 Week 07 的 `Week07_職缺情報站` 試算表，
在 `cleaned_data` 工作表新增以下欄位（在原有欄位後面）：

| 欄位名稱 | 說明 |
|---------|------|
| `job_description` | 職缺工作描述（手動填入測試資料）|
| `ai_level` | AI 分析的技能等級（Make 自動填入）|
| `ai_ie_score` | AI 評分的工管相關性（Make 自動填入）|
| `ai_skills` | AI 抽取的核心技能（Make 自動填入）|
| `ai_reason` | AI 分類理由（Make 自動填入）|
| `ai_status` | AI 分析狀態（待分析/已完成/分析失敗）|

**填入 5 筆測試用的職缺描述：**

| 列 | job_description |
|----|----------------|
| 2 | 誠徵工業工程師，需2年以上製程改善經驗，熟悉精實生產和六標準差，負責生產線平衡及瓶頸分析。 |
| 3 | 招募品質工程師，具備 IATF 16949 內部稽核經驗，熟悉 SPC 統計製程管制，無經驗可。 |
| 4 | 資深供應鏈管理師，10年以上採購及物流管理經驗，熟悉 ERP 系統，具備跨國供應商管理能力。 |
| 5 | 誠徵行銷企劃專員，具備社群媒體操作經驗，擅長文案撰寫及活動規劃，歡迎應屆畢業生。 |
| 6 | 製造工程師，負責新產品導入（NPI）及製程設計，需熟悉 FMEA 和 Control Plan，1年以上經驗。 |

<!-- 📸 截圖：試算表新增欄位，填入 5 筆測試職缺描述 -->

---

### 📌 任務 2-3：建立 Make 劇本

**劇本架構：**
```
[Google Sheets Watch Rows]（監控 ai_status = "待分析"）
    ↓
[HTTP POST → Gemini API 分析]
    ↓
[JSON Parse → 解析 AI 回傳結果]
    ↓
[JSON Parse 第二層 → 解析 AI 回傳的 JSON 字串]
    ↓
[Google Sheets Update Row → 回填分析結果]
    ↓
[Router → 根據 ie_score 分流]
    ├→ 高相關（4-5分）→ Discord #優質職缺推薦
    └→ 低相關（1-3分）→ 不推播（只更新試算表）
```

---

#### 節點一：Google Sheets Watch Rows

1. 新建劇本
2. 觸發器：**Google Sheets → Watch Rows**
3. 設定：
    - Spreadsheet：`Week07_職缺情報站`
    - Sheet：`cleaned_data`
    - Limit：`5`

> [!NOTE]
> **為什麼不用 Watch New Rows？**
> 因為我們要處理的是「已存在但尚未分析」的資料（`ai_status = 待分析`），
> 不是新進來的資料。Watch Rows 可以掃描現有的所有列。

---

#### 節點二：Filter — 只處理「待分析」的列

在 Watch Rows 後加入 **Filter**：

- 條件：`ai_status` Equal to `待分析`
- 確保只有待分析的資料才會送給 AI

---

#### 節點三：HTTP POST 呼叫 Gemini API

1. 新增「**HTTP → Make a request**」
2. 設定：

    | 設定項目 | 填入內容 |
    |---------|---------|
    | URL | `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=你的API_KEY` |
    | Method | `POST` |
    | Body type | `Raw` |
    | Content type | `application/json` |

3. Body 輸入（注意動態帶入 `job_description`）：
```json
    {
      "contents": [
        {
          "parts": [
            {
              "text": "請分析以下職缺描述，以 JSON 格式回傳分析結果。\n\nJSON 格式（嚴格遵守，只回傳 JSON，不要有其他文字或 markdown）：\n{\"level\": \"入門 或 中階 或 資深\", \"ie_score\": 整數1到5, \"skills\": [\"技能1\",\"技能2\",\"技能3\"], \"reason\": \"一句話說明（30字以內）\"}\n\n職缺描述：{{job_description}}"
            }
          ]
        }
      ],
      "generationConfig": {
        "temperature": 0.1,
        "maxOutputTokens": 500
      }
    }
```

    > `{{job_description}}` 是 Google Sheets Watch Rows 傳來的變數，
    > 對應試算表的 `job_description` 欄位。

4. **Parse response** → `Yes`

<!-- 📸 截圖：HTTP 模組設定，Body 包含動態變數 job_description -->

---

#### 節點四：取出 AI 回傳的文字

Gemini 的回應結構比較深，需要找到 AI 實際說的文字：

1. 新增「**JSON → Parse JSON**」
2. JSON String 填入：
```
    {{candidates[]:content.parts[]:text}}
```

    > 這個路徑依照 Gemini Response 的結構挖掘到最底層的文字。

---

#### 節點五：解析 AI 回傳的 JSON 字串

AI 回傳的是一個 **JSON 格式的字串**，
需要再解析一次才能取出各個欄位：

1. 再新增一個「**JSON → Parse JSON**」
2. JSON String 填入上一步解析出的文字變數

解析後，你可以取得：
- `{{level}}`
- `{{ie_score}}`
- `{{skills[]}}`（陣列）
- `{{reason}}`

> [!WARNING]
> **AI 有時會回傳不標準的 JSON（加了 ` ```json ` 代碼框）。**
> 如果 JSON Parse 失敗，在 Parse 之前先加一個：
>
> **Tools → Set Variable**，用以下公式清除代碼框：
> ```
> {{replace(replace(AItext; "```json"; ""); "```"; "")}}
> ```

---

#### 節點六：Google Sheets Update Row 回填結果

1. 新增「**Google Sheets → Update a Row**」
2. 設定：
    - Spreadsheet：`Week07_職缺情報站`
    - Sheet：`cleaned_data`
    - Row Number：Watch Rows 傳來的列號變數
3. 欄位對應：

    | 試算表欄位 | Make 變數 |
    |---------|---------|
    | `ai_level` | `{{level}}` |
    | `ai_ie_score` | `{{ie_score}}` |
    | `ai_skills` | `{{join(skills[]; "、")}}` |
    | `ai_reason` | `{{reason}}` |
    | `ai_status` | `已完成` |

    > `{{join(skills[]; "、")}}` 把技能陣列用頓號連接成字串，
    > 方便存入試算表單一儲存格。

<!-- 📸 截圖：Update Row 設定，顯示欄位對應關係 -->

---

#### 節點七：Router 根據 ie_score 分流

1. 新增「**Flow Control → Router**」
2. 設定兩條支線：

**支線 1 — 高相關（4-5分）：**
- Filter 條件：`ie_score` Greater than or equal to `4`
- 動作：Discord 推播到 `#優質職缺推薦` 頻道
```
    ⭐ **【AI 優質職缺推薦】**

    ▸ **職缺名稱：** {{job_title}}
    ▸ **公司：** {{company_name}}
    ▸ **地點：** {{location}}
    ▸ **薪資：** {{salary}}

    🤖 **AI 分析結果：**
    ▸ 技能等級：{{level}}
    ▸ 工管相關性：{{ie_score}}/5 ⭐
    ▸ 核心技能：{{ai_skills}}
    ▸ 分類理由：{{reason}}
```

**支線 2 — 低相關（1-3分）：**
- Filter 條件：`ie_score` Less than `4`
- 動作：不推播，直接結束（或記錄到「低相關職缺」工作表）

<!-- 📸 截圖：Make 完整劇本畫布，顯示所有節點和 Router 分流 -->

---

### 📌 任務 2-4：執行測試

1. 點擊「**Run once**」
2. 觀察 5 筆測試資料逐一被 AI 分析
3. 確認試算表的 `ai_level`、`ai_ie_score`、`ai_skills`、`ai_reason` 自動填入
4. 確認高相關職缺（ie_score ≥ 4）的 Discord 頻道收到推薦通知

<!-- 📸 截圖：試算表 AI 分析結果自動填入，五筆都顯示「已完成」 -->
<!-- 📸 截圖：Discord #優質職缺推薦 收到推薦通知 -->

> [!TIP]
> **🏆 第 2 小時 Checkpoint 完成！**
>
> - ✅ 在 Hoppscotch 成功測試 Gemini API
> - ✅ 在 Make 建立完整的 AI 分析劇本（七個節點）
> - ✅ AI 分析結果自動回填試算表
> - ✅ Router 根據 AI 評分分流，高相關職缺自動推播 Discord

---

### 🎯 第 2 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人作業**

---

#### 【練習 2-A】新增 AI 分析欄位（基礎）

修改 Prompt 和 Make 劇本，讓 AI 額外分析一個欄位：
```json
"suitable_year": "大二 或 大三 或 大四 或 研究生"
```

1. 在試算表新增 `ai_suitable_year` 欄位
2. 修改 HTTP 模組的 Prompt，加入這個欄位的要求
3. 修改 Update Row，把新欄位也回填到試算表

截圖試算表顯示新欄位已自動填入。

---

#### 【練習 2-B】錯誤處理（進階）

AI 偶爾會回傳格式不正確的 JSON（例如加了多餘文字），導致 JSON Parse 失敗。

為 JSON Parse 節點加入錯誤處理：
- 錯誤時把 `ai_status` 更新為 `分析失敗`
- 推播 Discord `#系統錯誤警報` 通知，包含：
    - 失敗的職缺名稱
    - AI 回傳的原始文字（方便除錯）

截圖加入錯誤處理後的劇本畫布。

---

#### 【練習 2-C】觀念問答（思考題）

請回答，貼到課堂共用文件：

> ❓ 本週的 AI 分析結果（level、ie_score）是由 Gemini 決定的，
> 不是由規則（if / else）決定的。
>
> 這帶來什麼潛在問題？
> 例如：同一個職缺每次分析結果可能不同嗎？
> 如果 AI 的判斷標準和系辦老師的判斷不一致，怎麼辦？
> 在什麼情況下你應該用 AI 判斷，什麼情況下應該用規則判斷？

---

## ⏰ 第 3 小時：整合 Week 07 + Week 08 — 完整的 AI 職缺情報系統

> 🎯 **本小時目標：** 把 Week 07 的爬蟲自動採集和 Week 08 的 AI 分析串接起來，
> 建立一套從「資料採集」到「AI 分析」到「智慧推薦」的完整自動化流水線。

---

### 💡 觀念：多劇本協作架構

目前我們有兩個獨立的 Make 劇本：
- **劇本 A（Week 07）**：每週一 ParseHub 採集 → 寫入 raw_data
- **劇本 B（Week 08）**：掃描待分析 → Gemini AI 分析 → 回填結果

如何讓它們串聯起來？
```
方案一：在劇本 A 結束後，自動觸發劇本 B
    優點：全自動
    缺點：如果劇本 A 失敗，劇本 B 也不會執行

方案二：讓劇本 B 用 Schedule 每天執行
    優點：即使劇本 A 延遲，劇本 B 還是會掃描和處理
    缺點：有時候執行劇本 B 但沒有新資料

方案三（本課程採用）：劇本 A 結束後用 HTTP Webhook 觸發劇本 B
    優點：即時串聯，又不會因為 A 失敗導致 B 錯誤
```

---

### 📌 任務 3-1：設定 Webhook 觸發劇本 B

**修改劇本 B 的觸發器：**

1. 開啟劇本 B（Week 08 的 AI 分析劇本）
2. 把觸發器從「Google Sheets Watch Rows」改為「**Webhooks → Custom webhook**」
3. 複製 Webhook URL（格式：`https://hook.eu1.make.com/xxx`）
4. 把觸發器設定儲存後，重新在後面接上原本的流程：
```
    [Webhook 觸發]
        ↓
    [Google Sheets Search Rows]（搜尋所有 ai_status = "待分析" 的列）
        ↓
    [Iterator]（逐筆處理）
        ↓
    [後續 AI 分析流程...]
```

> [!NOTE]
> **為什麼改用 Google Sheets Search Rows + Iterator？**
> Webhook 觸發後，我們需要主動去找所有待分析的資料，
> 而不是「等待新資料進來」。
> Search Rows 可以用條件搜尋，找到所有符合條件的列，
> 再用 Iterator 逐筆處理。

---

#### Google Sheets Search Rows 設定

1. 新增「**Google Sheets → Search Rows**」
2. 設定：
    - Spreadsheet：`Week07_職缺情報站`
    - Sheet：`cleaned_data`
    - Filter：`ai_status` = `待分析`
    - Limit：`20`（每次最多處理 20 筆）

---

#### Iterator 設定

1. 新增「**Flow Control → Iterator**」
2. Array：選擇 Search Rows 回傳的陣列

---

**修改劇本 A，在結束後觸發劇本 B：**

1. 開啟劇本 A（Week 07 的爬蟲劇本）
2. 在最後的 Discord 通知後，新增「**HTTP → Make a request**」
3. 設定：
    - URL：劇本 B 的 Webhook URL
    - Method：`POST`
    - Body：空（不需要傳資料，只是觸發）

<!-- 📸 截圖：劇本 A 最後新增 HTTP 模組，觸發劇本 B 的 Webhook -->

---

### 📌 任務 3-2：在試算表新增「每週新增」標記

當 ParseHub 爬取新資料後，如何讓系統知道哪些是「本週新增、尚未分析」的？

在劇本 A 的「Google Sheets Add a Row」節點，除了原本的職缺欄位，
額外新增：

| 欄位 | 值 |
|------|---|
| `ai_status` | `待分析` |
| `crawl_date` | `{{formatDate(now; "YYYY/MM/DD")}}` |

這樣每次劇本 A 寫入新資料時，`ai_status` 自動設為「待分析」，
劇本 B 就能找到並處理這些新資料。

---

### 📌 任務 3-3：建立完整系統的監控儀表板

在試算表新增工作表 `system_dashboard`，
用公式顯示整個系統的運作狀態：
```
本週爬取職缺總數：
=COUNTIF(cleaned_data!K:K, TODAY()&"*")
（K 欄是 crawl_date，統計今天的資料）

待分析職缺數：
=COUNTIF(cleaned_data!L:L, "待分析")

已完成分析數：
=COUNTIF(cleaned_data!L:L, "已完成")

分析失敗數：
=COUNTIF(cleaned_data!L:L, "分析失敗")

AI 推薦優質職缺數（4-5分）：
=COUNTIF(cleaned_data!M:M, ">=4")

平均工管相關性評分：
=AVERAGEIF(cleaned_data!L:L, "已完成", cleaned_data!M:M)

最常出現的技能（前三名）：
（手動觀察，或用進階公式）
```

<!-- 📸 截圖：system_dashboard 工作表，顯示系統運作指標 -->

---

### 📌 任務 3-4：手動測試完整流程

模擬完整的週期：

1. 在 `cleaned_data` 手動新增 3 筆新職缺，`ai_status` 設為「待分析」
2. 手動觸發劇本 B 的 Webhook（在 Make 點擊「Run once」）
3. 觀察：
    - `cleaned_data` 的 `ai_status` 是否從「待分析」變成「已完成」
    - `ai_level`、`ai_ie_score` 等欄位是否自動填入
    - 高分職缺是否推播到 Discord `#優質職缺推薦`
    - `system_dashboard` 的數字是否更新

<!-- 📸 截圖：完整測試後的 system_dashboard，各項指標正確顯示 -->

> [!TIP]
> **🏆 第 3 小時 Checkpoint 完成！**
>
> - ✅ 用 Webhook 串聯兩個劇本，實現全自動流水線
> - ✅ ParseHub 採集 → 自動標記「待分析」→ Gemini 分析 → 結果回填
> - ✅ 建立系統監控儀表板，一眼掌握整個系統的運作狀況

---

### 🎯 第 3 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人 + 分組**

---

#### 【練習 3-A】Router 三路分流（個人，基礎）

把 Router 從兩條支線擴充為三條：

| 支線 | 條件 | 動作 |
|------|------|------|
| 高相關 | ie_score ≥ 4 | 推播 Discord `#優質職缺推薦` |
| 中相關 | ie_score = 3 | 推播 Discord `#一般職缺參考` |
| 低相關 | ie_score ≤ 2 | 記錄到試算表「低相關紀錄」，不推播 |

截圖三條支線的 Router 設定和三個 Discord 頻道各自收到通知的畫面。

---

#### 【練習 3-B】整合挑戰（分組，2～3 人）

**設計「工管系畢業生就業市場 AI 分析週報」系統：**

完整流水線需求：
1. **ParseHub**（Week 07）每週一自動採集職缺資料
2. **Gemini AI**（Week 08）自動分析每筆職缺的等級和相關性
3. **Google Sheets** 存放所有資料，`system_dashboard` 顯示統計
4. **Looker Studio**（Week 06 建立的）嵌入 Notion 網站（Week 04 建立的）
5. **Make 週報劇本**：每週五下午 5 點自動發送 Discord 週報，內容包含：
    - 本週採集職缺總數
    - AI 推薦優質職缺數
    - 本週最熱門的三個技能關鍵字
    - 平均薪資下限
    - 本週薪資最高的一筆優質職缺

繳交：系統架構圖（手繪或 Notion 頁面）+ Make 劇本截圖 + Discord 週報截圖

---

#### 【練習 3-C】延伸思考（挑戰）

> ❓ 目前的 AI 分析是對「職缺描述」做分析。
> 但如果我們把學生的「個人履歷」也加進來，
> 讓 AI 同時比對「職缺要求」和「學生能力」，
> 自動判斷「這個學生適不適合這個職缺」，
> 這樣的系統從技術和隱私兩個角度各有什麼挑戰？
>
> 請提出你的設計方案，並說明你如何在「自動化效率」和「隱私保護」之間取得平衡。

---

## 🏆 本週總結

### 📊 本週技能地圖
```
【Prompt Engineering】
    ├── 五要素：角色/任務/格式/限制/輸入
    ├── 結構化輸出：讓 AI 回傳 JSON
    └── 版本比較：體驗不同 Prompt 的差異

        ↓ 設計好 Prompt

【Gemini API 串接】
    ├── temperature 參數：控制隨機程度
    ├── Hoppscotch 測試：確認 API 格式
    ├── Make HTTP 模組：動態帶入 job_description
    └── 雙層 JSON Parse：解析 AI 回傳的 JSON 字串

        ↓ AI 分析結果

【Router 智慧分流】
    ├── ie_score ≥ 4 → Discord 推薦通知
    ├── ie_score < 4 → 只更新試算表
    └── 錯誤處理：AI 格式錯誤時的防呆機制

        ↓ 整合兩週系統

【完整流水線】
    ParseHub 採集 → 標記待分析
        → Webhook 觸發 AI 分析劇本
        → Gemini 逐筆分析
        → 結果回填試算表
        → Router 分流推播
        → system_dashboard 即時監控
```

### 🔄 本週和前後週的連結
```
Week 05（API 深潛）：學了 API 請求結構和 JSON 格式
    ↓ 本週直接應用
    → Gemini API 的請求結構、雙層 JSON Parse

Week 07（爬蟲）：自動採集職缺資料
    ↓ 本週接上
    → 爬下來的資料送給 AI 分析，完整流水線

Week 10（下下週）：全自動情報局
    ↓ 本週是技術基礎
    → 情報局用同樣的架構：爬蟲採集 → AI 摘要 → 推播
    → 技能完全可以直接複用
```

### 🤔 課後思考問題

1. **AI 的一致性問題：** 你設定了 `temperature: 0.1`，
   但 AI 還是可能對同一份職缺給出不同的 `ie_score`。
   如果這個系統是給學校官方使用，
   你會如何設計「人工複核機制」，讓老師可以修正 AI 的錯誤判斷？

2. **成本估算：** Gemini API 免費版有請求次數限制。
   假設你的課程有 30 名學生，每週採集 100 筆職缺，
   一個學期（14 週）共需要呼叫 Gemini API 幾次？
   請計算並確認是否超出免費額度。

3. **AI 偏見：** 這套系統的 AI 分類標準是你在 Prompt 裡定義的。
   如果你的 Prompt 對某類職缺有系統性的偏見（例如：不重視藍領技術工作），
   這個偏見會如何影響學生的求職決策？
   作為系統設計者，你有什麼責任？

### 📚 本週自學資源

| 資源 | 用途 |
|------|------|
| [Google AI Studio](https://aistudio.google.com/) | Gemini Prompt 測試環境 |
| [Gemini API 文件](https://ai.google.dev/docs) | 完整 API 參數說明 |
| [Prompt Engineering 指南（Google）](https://ai.google.dev/docs/prompt_best_practices) | 官方最佳實踐 |
| [JSON Formatter](https://jsonformatter.curiousconcept.com/) | 驗證和格式化 JSON |

---

### 📝 下週預告

> 🔜 **Week 09：數據戰情室 — Looker Studio 進階 × 視覺化工具大比拼**
>
> - Looker Studio 進階：混合資料源、Calculated Field、Blend Data
> - 把 Week 07 的職缺資料和 Week 08 的 AI 分析結果合併成一個儀表板
> - 視覺化工具比較實戰：Looker Studio vs Flourish vs Datawrapper
> - 哪種圖表適合哪種資料？選錯圖表讓資料說謊
>
> **課前任務：**
> 確認你的 Looker Studio 帳號可以正常登入（用 Google 帳號即可），
> 並把 Week 07 的 `Week07_職缺情報站` 試算表設定為「知道連結的人可以檢視」。

---

*雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系 ｜ 2026 春季學期*
*Week 08 of 14*
