# 🔌 Week 05：API 深潛 — REST API × JSON × HTTP 方法

**雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系**

> **本週課程理念：** Week 01 你已經成功呼叫了 OCR.space API——
> 但你知道為什麼要用 POST 而不是 GET 嗎？
> 為什麼 `apikey` 要放在 Body 而不是 URL？
> JSON 格式到底是什麼？
>
> **今天我們補上理論，讓你從「照著做」進化到「知道為什麼這樣做」。**
> 理解了原理，你就能串接任何 API，不再依賴教學文件。

---

## 🎯 本週學習目標

學完本週，你將能夠：
- ✅ 用白話解釋什麼是 REST API、端點（Endpoint）、請求與回應
- ✅ 分辨 HTTP 方法（GET / POST / PUT / DELETE）的差異與適用情境
- ✅ 讀懂 JSON 格式，並手動撰寫簡單的 JSON 資料
- ✅ 用 **Hoppscotch**（免費線上工具）親手呼叫真實 API，不需要寫任何程式碼
- ✅ 把 API 知識回頭應用到 Make，解釋 Week 01 每個設定的原因

---

## 🏢 本週實作情境：【API 偵探任務】

> 🔍 **你的角色：** 你是工管系「數位化委員會」的 API 審查員。
> 系上每季都會引進新的雲端服務，你的任務是在串接到 Make 之前，
> 先「手動測試」這些 API，確認它們正常運作、回傳的資料格式正確。
>
> **今天要測試三個 API：**
> 1. 公開天氣 API（練習 GET 請求）
> 2. JSONPlaceholder 測試 API（練習 POST 請求）
> 3. 回頭驗證 OCR.space API（把 Week 01 的設定用理論解釋一遍）

---

## ⏰ 第 1 小時：API 與 HTTP 基礎概念

> 🎯 **本小時目標：** 建立對 API 和 HTTP 的正確心智模型，
> 讓你能用日常語言解釋這些概念。

---

### 💡 觀念一：什麼是 API？

**API（Application Programming Interface）** 是兩個軟體之間的「服務窗口」。

現實比喻：
```
你（客人）        服務生（API）        廚房（伺服器）
    │                  │                   │
    │ 我要一份牛排      │                   │
    │ ─────────────→   │                   │
    │                  │ 牛排，5分熟，不加醬 │
    │                  │ ─────────────────→│
    │                  │                   │ 準備中...
    │                  │         ←─────────│
    │ 這是你的牛排      │                   │
    │ ←─────────────   │                   │
```

| 現實場景 | API 對應 |
|---------|---------|
| 你（客人）| 你的程式 / Make |
| 服務生 | API |
| 廚房 | 伺服器（資料庫、運算系統）|
| 你的點餐 | HTTP Request（請求）|
| 送來的牛排 | HTTP Response（回應）|

---

### 💡 觀念二：什麼是 REST API？

**REST（Representational State Transfer）** 是目前最普遍的 API 設計規範。

符合 REST 規範的 API 稱為 **RESTful API**，它有幾個特徵：

1. **用 URL 表示資源**
```
    https://api.example.com/users          → 所有使用者
    https://api.example.com/users/42       → 第 42 號使用者
    https://api.example.com/users/42/posts → 第 42 號使用者的所有文章
```

2. **用 HTTP 方法表示動作**

    | HTTP 方法 | 動作 | 白話說 |
    |---------|------|-------|
    | **GET** | 讀取 | 給我看這個資源 |
    | **POST** | 新增 | 幫我建立一個新的資源 |
    | **PUT** | 更新（全部） | 把這個資源整個換掉 |
    | **PATCH** | 更新（部分） | 只改這個資源的某些欄位 |
    | **DELETE** | 刪除 | 刪掉這個資源 |

3. **用 HTTP 狀態碼表示結果**

    | 狀態碼 | 意思 | 常見情境 |
    |--------|------|---------|
    | **200** | 成功 | 一切正常 |
    | **201** | 已建立 | POST 新增成功 |
    | **400** | 請求錯誤 | 你傳的資料格式有問題 |
    | **401** | 未授權 | API Key 錯誤或未提供 |
    | **403** | 禁止存取 | 你沒有權限 |
    | **404** | 找不到 | 這個資源不存在 |
    | **429** | 太多請求 | 超過 API 速率限制 |
    | **500** | 伺服器錯誤 | 對方的系統出問題了 |

> [!NOTE]
> **為什麼要記狀態碼？**
> Week 03 的錯誤處理中，`{{error.message}}` 通常包含狀態碼。
> 看到 `401` 你就知道是 API Key 問題，看到 `429` 就知道要降低呼叫頻率。
> 這讓你能快速診斷問題，不需要每次都去查文件。

---

### 💡 觀念三：什麼是 JSON？

**JSON（JavaScript Object Notation）** 是 API 資料交換最常用的格式。

把 JSON 想成是**有格式的文字**，讓電腦和人類都能讀懂：
```json
{
  "姓名": "王大明",
  "學號": "1112001",
  "實習類型": "國內企業",
  "企業清單": ["台積電", "聯電", "鴻海"],
  "是否核准": true,
  "月薪": 32000
}
```

**JSON 的六種資料類型：**

| 類型 | 範例 | 說明 |
|------|------|------|
| **String（字串）** | `"王大明"` | 用雙引號包住的文字 |
| **Number（數字）** | `32000` | 不用引號 |
| **Boolean（布林）** | `true` / `false` | 只有這兩個值 |
| **Array（陣列）** | `["台積電", "聯電"]` | 用方括號，多個值 |
| **Object（物件）** | `{"key": "value"}` | 用大括號，鍵值對 |
| **Null（空值）** | `null` | 表示沒有值 |

**常見錯誤：**
```json
// ❌ 錯誤：用單引號
{'姓名': '王大明'}

// ❌ 錯誤：最後一個元素有逗號
{"姓名": "王大明", "學號": "1112001",}

// ✅ 正確
{"姓名": "王大明", "學號": "1112001"}
```

---

### 📌 任務 1-1：安裝並認識 Hoppscotch

**工具：** [Hoppscotch](https://hoppscotch.io/)（完全免費，不需要安裝，瀏覽器直接用）

1. 前往 [hoppscotch.io](https://hoppscotch.io/)
2. 不需要登入，直接使用
3. 認識介面的四個區域：
```
┌─────────────────────────────────────────────┐
│  方法 [GET▼]  URL 欄位                [Send] │  ← 請求設定區
├─────────────────────────────────────────────┤
│  Params │ Headers │ Body │ Auth             │  ← 請求細節區
├─────────────────────────────────────────────┤
│  Response                                   │  ← 回應顯示區
│  Status: 200 OK                             │
│  {"result": "..."}                          │
└─────────────────────────────────────────────┘
```

<!-- 📸 截圖：Hoppscotch 介面初始畫面 -->

---

### 📌 任務 1-2：第一個 API 呼叫 — GET 天氣資料

**API：** Open-Meteo（完全免費，不需要 API Key）

1. 在 Hoppscotch 的方法下拉選 **GET**
2. URL 欄位輸入：
```
    https://api.open-meteo.com/v1/forecast?latitude=24.43&longitude=118.32&current=temperature_2m,wind_speed_10m
```

3. 點擊「**Send**」
4. 觀察右側 Response 區域出現的 JSON 資料

**Response 解析：**
```json
{
  "latitude": 24.43,
  "longitude": 118.32,
  "current": {
    "temperature_2m": 26.4,
    "wind_speed_10m": 12.3
  }
}
```

> [!NOTE]
> **URL 解析：**
> ```
> https://api.open-meteo.com/v1/forecast
>     ?latitude=24.43          ← 金門的緯度
>     &longitude=118.32        ← 金門的經度
>     &current=temperature_2m  ← 要哪些資料（當前氣溫）
>     ,wind_speed_10m          ← 加逗號表示要多個欄位
> ```
> URL 裡 `?` 後面的部分叫做 **Query String（查詢參數）**，
> 這是 GET 請求傳遞資料的方式——直接夾在 URL 裡。

<!-- 📸 截圖：Hoppscotch 呼叫天氣 API 成功，顯示 JSON 回應 -->

---

### 📌 任務 1-3：在 Hoppscotch 的 Params 分頁設定參數

URL 裡的查詢參數也可以在 **Params** 分頁填寫，效果完全相同：

1. 清空 URL 欄位，只保留：
```
    https://api.open-meteo.com/v1/forecast
```
2. 點擊「**Params**」分頁
3. 新增以下參數：

    | Key | Value |
    |-----|-------|
    | `latitude` | `24.43` |
    | `longitude` | `118.32` |
    | `current` | `temperature_2m,wind_speed_10m` |

4. 點擊「**Send**」，確認結果和剛才一樣

> [!TIP]
> **Params 分頁 vs 直接寫在 URL 裡：**
> 效果完全相同，Params 分頁只是讓你看得更清楚，
> Hoppscotch 會自動把它們組合成正確的 URL 格式。
> 在 Make 的 HTTP 模組裡，也有一樣的 Query Parameters 設定。

<!-- 📸 截圖：Hoppscotch Params 分頁填寫參數後的畫面 -->

> [!TIP]
> **🏆 第 1 小時 Checkpoint 完成！**
>
> - ✅ 能用白話解釋 API、REST、HTTP 方法、狀態碼
> - ✅ 能讀懂 JSON 格式，知道六種資料類型
> - ✅ 成功用 Hoppscotch 呼叫第一個真實 API，取得金門即時天氣資料

---

### 🎯 第 1 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 1-A】改變 API 參數（基礎）

修改天氣 API 的請求，加入更多資料欄位：

1. 在 `current` 參數加入 `relative_humidity_2m`（相對濕度）和 `precipitation`（降雨量）
2. 新增一個參數 `timezone=Asia/Taipei`，讓時間顯示台灣時區

更新後的 URL 應該類似：
```
https://api.open-meteo.com/v1/forecast?latitude=24.43&longitude=118.32&current=temperature_2m,wind_speed_10m,relative_humidity_2m,precipitation&timezone=Asia/Taipei
```

截圖 Response 顯示新增欄位的結果。

---

#### 【練習 1-B】手寫 JSON（進階）

不用任何工具，純手動在記事本寫出以下資料的 JSON 格式：

> 一份實習申請資料：
> - 姓名：陳小明
> - 學號：1113005
> - 實習類型：海外實習
> - 申請的企業清單（陣列）：日立製作所、豐田汽車
> - 是否已取得簽證：否（用布林值）
> - 預計實習天數：90
> - 指導教授資訊（巢狀物件）：姓名為「林教授」，分機為「2345」

寫完後貼到 [jsonlint.com](https://jsonlint.com/) 驗證格式是否正確，截圖驗證通過的畫面。

---

#### 【練習 1-C】狀態碼診斷（思考題）

以下三個錯誤場景，請說明最可能的狀態碼是什麼，以及如何解決：

1. 你把 API Key 打錯了
2. 你要查詢的使用者 ID 不存在
3. 你的 Make 劇本在 1 分鐘內呼叫 API 超過 100 次

---

## ⏰ 第 2 小時：POST 請求與 Headers — API 的身份驗證

> 🎯 **本小時目標：** 學會 POST 請求的結構，
> 理解 API Key 的各種傳遞方式，並用理論重新解釋 Week 01 的 OCR.space 設定。

---

### 💡 觀念一：GET vs POST 的核心差異
```
GET 請求：資料放在 URL 裡（公開可見）
    https://api.example.com/search?q=台積電&page=1
    └─ 所有人看網址就能看到你在搜尋什麼

POST 請求：資料放在 Body 裡（不在 URL 裡）
    https://api.example.com/apply
    Body: {"姓名": "王大明", "密碼": "mypassword"}
    └─ 資料在請求內容裡，URL 看不到
```

**選擇原則：**

| 情境 | 用 GET | 用 POST |
|------|--------|---------|
| 查詢資料 | ✅ | |
| 新增資料 | | ✅ |
| 傳送敏感資訊（密碼、API Key） | ❌ 不安全 | ✅ |
| 上傳檔案 | ❌ 無法 | ✅ |
| 可以被書籤收藏的請求 | ✅ | ❌ |

---

### 💡 觀念二：API Key 的三種傳遞方式

不同的 API 設計者選擇不同的 Key 傳遞位置：

**方式 1：放在 URL 的 Query String**
```
https://api.example.com/data?apikey=K84827219788957
```
⚠️ 最不安全，Key 暴露在 URL 裡，會被記錄在伺服器 Log 中

**方式 2：放在 Headers**
```
GET https://api.example.com/data
Headers:
  Authorization: Bearer K84827219788957
```
✅ 最常見、最安全，Key 不在 URL 裡

**方式 3：放在 Body（表單欄位）**
```
POST https://api.example.com/parse
Body (multipart/form-data):
  apikey = K84827219788957
  file = [圖片檔案]
```
✅ 上傳檔案時使用，OCR.space 用這種方式

> [!NOTE]
> **這就是為什麼 Week 01 的 OCR.space 要這樣設定！**
>
> OCR.space 需要同時接收：
> - `apikey`（身份驗證）
> - `file`（圖片檔案二進位資料）
>
> 只有 `multipart/form-data` 格式的 POST 請求才能同時傳送文字和檔案，
> 所以 Body type 必須選 `multipart/form-data`，不能選 `JSON`。

---

### 📌 任務 2-1：POST 請求練習 — JSONPlaceholder

**API：** [JSONPlaceholder](https://jsonplaceholder.typicode.com/)（免費測試用 API，不需要 Key）

這個 API 模擬一個「部落格系統」，提供文章、留言、使用者等假資料，
是練習 HTTP 方法最好的工具。

#### 步驟一：先用 GET 查詢現有文章

1. 方法選 **GET**
2. URL：`https://jsonplaceholder.typicode.com/posts/1`
3. 點擊「**Send**」

Response：
```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere...",
  "body": "quia et suscipit..."
}
```

<!-- 📸 截圖：GET 查詢單篇文章的結果 -->

---

#### 步驟二：用 POST 新增一篇文章

1. 方法改為 **POST**
2. URL：`https://jsonplaceholder.typicode.com/posts`
3. 點擊「**Body**」分頁 → 選擇 **raw** → 右側格式選 **JSON**
4. 輸入以下 JSON：
```json
    {
      "title": "工管系實習申請系統說明",
      "body": "本系統整合 Google 表單、AutoCrat 與 Make，實現實習申請全流程自動化。",
      "userId": 1
    }
```

5. 點擊「**Send**」

Response（注意狀態碼是 **201 Created**）：
```json
{
  "title": "工管系實習申請系統說明",
  "body": "本系統整合 Google 表單...",
  "userId": 1,
  "id": 101
}
```

> [!NOTE]
> 伺服器回傳的 JSON 比你送出的多了一個 `"id": 101`，
> 這是伺服器自動分配的新資源 ID。
> 這是 RESTful API 的標準行為——新增成功後，
> 伺服器告訴你「你的資源被分配到 ID 101」。

<!-- 📸 截圖：POST 新增文章成功，狀態碼 201 -->

---

#### 步驟三：用 Headers 傳遞 Content-Type

如果你的 POST 請求沒有指定 Content-Type，
伺服器可能不知道 Body 裡的資料是什麼格式。

1. 點擊「**Headers**」分頁
2. 新增一個 Header：

    | Key | Value |
    |-----|-------|
    | `Content-Type` | `application/json` |

3. 重新送出，確認伺服器正確解析

> [!NOTE]
> **Content-Type 是什麼？**
> 是你告訴伺服器「我送給你的 Body 資料是什麼格式」的 Header。
>
> | Content-Type | 對應格式 |
> |-------------|---------|
> | `application/json` | JSON 格式 |
> | `multipart/form-data` | 表單格式（可含檔案）|
> | `application/x-www-form-urlencoded` | 傳統表單格式 |
>
> 這就是 Week 01 Make 裡 **Body content type** 設定的意義！

---

### 📌 任務 2-2：用理論重新解釋 Week 01 的 OCR.space 設定

現在你有了足夠的理論知識，可以解釋 Week 01 的每一個設定：

打開你在 Week 01 的 Make 截圖（HTTP Make a Request 節點），
對照以下解析表，確認每個設定你都能說出原因：

| Make 設定 | 值 | 原因 |
|---------|---|------|
| URL | `https://api.ocr.space/parse/image` | OCR.space 的 API 端點（Endpoint） |
| Method | `POST` | 需要上傳圖片檔案，GET 無法傳送檔案 |
| Authentication type | `No authentication` | OCR.space 的 Key 放在 Body 裡，不用 Make 的 Auth 機制 |
| Body content type | `multipart/form-data` | 唯一能同時傳送文字（apikey）和檔案的格式 |
| Field 1 Name: `apikey` | 你的 Key | 身份驗證，放在 Body 而非 URL，避免 Key 洩漏 |
| Field 2 Name: `file` | 圖片 Data | 圖片的二進位內容 |
| Field 2 File name: `receipt.jpg` | `receipt.jpg` | 告訴 OCR.space 這是 jpg 格式，否則無法判斷檔案類型 |
| Field 3 Name: `language` | `cht` | 指定辨識繁體中文（cht = Chinese Traditional） |
| Parse response | `Yes` | 讓 Make 自動把 JSON 字串解析成結構化變數 |

<!-- 📸 截圖：Week 01 的 OCR.space Make 截圖，旁邊手寫（或截圖）說明每個設定的原因 -->

> [!TIP]
> **🏆 第 2 小時 Checkpoint 完成！**
>
> - ✅ 理解 GET vs POST 的本質差異
> - ✅ 知道 API Key 的三種傳遞方式及各自的安全性
> - ✅ 成功用 POST 新增資源，並看懂 201 狀態碼
> - ✅ 能用 API 理論完整解釋 Week 01 的每一個 OCR.space 設定

---

### 🎯 第 2 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：15 分鐘｜個人作業**

---

#### 【練習 2-A】PUT 和 DELETE 練習（基礎）

用 JSONPlaceholder 繼續練習其他 HTTP 方法：

**PUT（更新文章）：**
- 方法：PUT
- URL：`https://jsonplaceholder.typicode.com/posts/1`
- Body：
```json
    {
      "id": 1,
      "title": "更新後的標題",
      "body": "更新後的內容",
      "userId": 1
    }
```
- 截圖回應，確認狀態碼為 200

**DELETE（刪除文章）：**
- 方法：DELETE
- URL：`https://jsonplaceholder.typicode.com/posts/1`
- 不需要 Body
- 截圖回應，確認狀態碼為 200，Body 為空 `{}`

---

#### 【練習 2-B】Authorization Header 練習（進階）

有些 API 的 Key 是放在 Headers 的 `Authorization` 欄位。
練習在 Hoppscotch 的 Headers 分頁模擬這種情境：

1. URL：`https://jsonplaceholder.typicode.com/posts`
2. 方法：GET
3. 在 Headers 新增：
    - Key：`Authorization`
    - Value：`Bearer my-fake-api-key-12345`
4. 送出請求，觀察結果

> JSONPlaceholder 不會真的驗證這個 Key，但你已經學會了如何傳送。
> 截圖 Headers 分頁設定和 Response 結果。

---

#### 【練習 2-C】API 文件閱讀練習（思考題）

前往 [OCR.space API 文件](https://ocr.space/ocrapi)，找到以下問題的答案：

1. 除了 `cht`（繁體中文），還有哪個 language code 是繁體中文？
2. `OCREngine` 參數有幾個選項？各自適合什麼情境？
3. 免費版 API Key 的每月請求上限是多少次？

貼到課堂共用文件，並說明你是在文件的哪個段落找到這些資訊。

---

## ⏰ 第 3 小時：把 API 知識用回 Make — 串接新的公開 API

> 🎯 **本小時目標：** 不靠任何教學文件，
> 自己閱讀 API 文件後，在 Make 裡成功串接一個從未用過的 API。

---

### 💡 觀念：如何閱讀 API 文件？

任何 API 文件都會包含這些資訊：
```
1. Base URL（基礎網址）
   → 這是所有請求的前綴

2. Endpoints（端點）
   → 各種功能的 URL 路徑

3. Authentication（身份驗證）
   → API Key 要放在哪裡

4. Request Parameters（請求參數）
   → 必填和選填的參數有哪些

5. Response Format（回應格式）
   → 回傳的 JSON 結構長什麼樣
```

**閱讀 API 文件的 SOP：**
```
第一步：找 Authentication → 知道 Key 怎麼傳
第二步：找你要用的 Endpoint → 知道 URL 是什麼
第三步：找 Required Parameters → 知道一定要填什麼
第四步：看 Response Example → 知道回傳資料的結構
第五步：在 Hoppscotch 測試 → 確認設定正確
第六步：搬到 Make → 照著 Hoppscotch 的設定填
```

---

### 📌 任務 3-1：串接匯率 API — 從文件到 Make

**API：** [ExchangeRate-API](https://www.exchangerate-api.com/)
（免費版每月 1,500 次請求，不需要信用卡）

#### 步驟一：申請免費 API Key

1. 前往 [exchangerate-api.com](https://www.exchangerate-api.com/)
2. 輸入 Email → 點擊「**Get Free Key**」
3. 確認 Email，取得 API Key

<!-- 📸 截圖：ExchangeRate-API 取得 API Key 畫面（Key 遮住後五碼）-->

---

#### 步驟二：閱讀文件，找到端點

前往 [文件頁面](https://www.exchangerate-api.com/docs/standard-requests)，找到：

| 資訊 | 內容 |
|------|------|
| Endpoint | `https://v6.exchangerate-api.com/v6/YOUR-API-KEY/pair/TWD/JPY` |
| 方法 | GET |
| 說明 | 查詢兩種貨幣之間的匯率 |

---

#### 步驟三：在 Hoppscotch 測試

1. 方法：**GET**
2. URL：把 `YOUR-API-KEY` 換成你的真實 Key：
```
    https://v6.exchangerate-api.com/v6/你的Key/pair/TWD/JPY
```
3. 送出，確認回應包含匯率資料

Response 範例：
```json
{
  "result": "success",
  "base_code": "TWD",
  "target_code": "JPY",
  "conversion_rate": 4.52
}
```

<!-- 📸 截圖：Hoppscotch 成功取得 TWD 兌 JPY 匯率 -->

---

#### 步驟四：搬到 Make

不靠任何教學，只憑你現在的知識，在 Make 裡串接這個 API：

1. 新建劇本，觸發器選 **Schedule**（定時執行，設為每天早上 9:00）
2. 新增 **HTTP → Make a request**
3. 根據你在 Hoppscotch 測試成功的設定填入 Make：

    | 設定 | 填入內容 |
    |------|---------|
    | URL | 你測試成功的完整 URL |
    | Method | GET |

4. 新增 **JSON → Parse JSON**，帶入 HTTP 回傳的 `Data`
5. 新增 **Discord → Send a Message by Webhook Bot**，訊息填入：
```
    💱 **【每日匯率通知】**

    📅 更新時間：{{formatDate(now; "YYYY/MM/DD HH:mm")}}

    ▸ 新台幣 → 日圓：1 TWD = {{conversion_rate}} JPY

    資料來源：ExchangeRate-API
```

6. 開啟劇本排程，等待自動執行

<!-- 📸 截圖：Make 完整劇本畫布（Schedule → HTTP → JSON → Discord） -->
<!-- 📸 截圖：Discord 收到每日匯率通知 -->

---

### 📌 任務 3-2：把匯率資訊回寫到 Google Sheets

在 Discord 通知之後，再新增一個 **Google Sheets → Add a Row**，
把每天的匯率記錄下來，方便追蹤趨勢：

建立一個新試算表「**匯率追蹤記錄**」，欄位設定：

| 欄位 | 資料來源 |
|------|---------|
| 日期 | `{{formatDate(now; "YYYY/MM/DD")}}` |
| 基礎貨幣 | `{{base_code}}` |
| 目標貨幣 | `{{target_code}}` |
| 匯率 | `{{conversion_rate}}` |

<!-- 📸 截圖：Google Sheets 每日自動記錄匯率的試算表 -->

> [!NOTE]
> **這個練習的教學意義：**
> 你剛才完成的流程是：
> ```
> 閱讀 API 文件 → Hoppscotch 測試 → 搬到 Make → 串接 Discord + Sheets
> ```
> 這個 SOP 適用於任何 API！
> 往後不管是什麼新 API，你都有能力自己串接，
> 不需要依賴別人寫好的教學。

> [!TIP]
> **🏆 第 3 小時 Checkpoint 完成！**
>
> - ✅ 學會閱讀 API 文件的 SOP
> - ✅ 從文件到 Hoppscotch 測試，再到 Make 串接，獨立完成全流程
> - ✅ 建立每日自動匯率通知 + 試算表記錄系統
> - ✅ 確認「會用 API」的能力可以泛化到任何未知的 API

---

### 🎯 第 3 小時學生練習題

> [!NOTE]
> **⏱️ 練習時間：20 分鐘｜個人 + 分組**

---

#### 【練習 3-A】換一組貨幣（個人，基礎）

修改你的匯率劇本，改成追蹤 **TWD → USD（美元）** 的匯率：

1. 修改 HTTP 模組的 URL，把 `JPY` 換成 `USD`
2. 修改 Discord 訊息內容
3. 手動執行一次，截圖 Discord 收到美元匯率通知的畫面

---

#### 【練習 3-B】串接你自己找的 API（個人，進階）

從以下清單選一個免費 API，獨立完成從文件閱讀到 Make 串接的完整流程：

| API | 功能 | 文件網址 |
|-----|------|---------|
| wttr.in | 天氣（不需要 Key）| `wttr.in/:city?format=j1` |
| RestCountries | 國家資訊（不需要 Key）| `restcountries.com/v3.1/name/{name}` |
| PokeAPI | 寶可夢資料（不需要 Key）| `pokeapi.co/api/v2/pokemon/{id}` |

要求：
1. 在 Hoppscotch 成功取得資料（截圖）
2. 在 Make 串接並推播到 Discord（截圖）
3. 用表格說明你閱讀文件後找到的四個關鍵資訊（Base URL、Endpoint、Auth、Response 結構）

---

#### 【練習 3-C】整合挑戰（分組，2～3 人）

情境：你們小組要為「工管系廠商實習處理中心」新增一個「即時資訊看板」功能，
在 Notion 網站（Week 04 建立的）上嵌入以下自動更新的資訊：

1. **即時匯率**（給海外實習學生參考）：用本週建立的匯率 API
2. **即時天氣**（金門）：用 Week 01 的 Open-Meteo API
3. 兩個資訊每天自動更新到 Google Sheets
4. 把 Google Sheets 用 Looker Studio 做成儀表板，嵌入 Notion 頁面

繳交：
- Make 劇本截圖（兩個 API 串接）
- Notion 頁面網址（含嵌入的 Looker Studio 儀表板）

---

## 🏆 本週總結

### 📊 本週技能地圖
```
【API 基礎理論】
    ├── REST API 概念：資源、端點、方法、狀態碼
    ├── HTTP 方法：GET（查詢）/ POST（新增）/ PUT（更新）/ DELETE（刪除）
    └── JSON 格式：六種資料類型，如何讀懂和手寫

        ↓ 理論 → 實作

【Hoppscotch 實戰】
    ├── GET：天氣 API（Query String 參數）
    ├── POST：JSONPlaceholder（Body + Headers）
    └── 解析 Week 01 OCR.space 每個設定的原因

        ↓ Hoppscotch → Make

【獨立串接新 API】
    ├── 閱讀 API 文件的 SOP
    ├── ExchangeRate-API：每日匯率自動通知
    └── 資料自動記錄到 Google Sheets
```

### 🔄 API 知識和前幾週的連結
```
Week 01：用了 HTTP 模組但不知道為什麼
    ↓ Week 05 補上理論
    → 現在你能解釋 Week 01 每一個設定的原因

Week 03：錯誤處理的狀態碼（400、401、429）
    ↓ Week 05 解釋了狀態碼的意義
    → 現在看到 401 就知道是 Key 問題，429 就知道要降頻率

Week 07（下下週）：ParseHub 爬蟲 → Make 串接
    ↓ 有了 Week 05 的 API 基礎
    → 看得懂 ParseHub 的 API 文件，能自己設定參數
```

### 🤔 課後思考問題

1. **安全性思考：** 如果你的 API Key 不小心被推到公開的 GitHub Repository，會發生什麼事？你應該採取什麼緊急措施？（提示：搜尋「API Key rotation」和「GitHub secret scanning」）

2. **速率限制的商業邏輯：** ExchangeRate-API 免費版每月 1,500 次請求，付費版無限次。從商業角度，為什麼 API 提供者要設定速率限制而不是完全免費？這對你設計系統有什麼啟發？

3. **RESTful 設計原則：** 以下兩個 API 設計，哪個更符合 RESTful 規範？為什麼？
    - 設計 A：`POST /deleteUser?id=42`
    - 設計 B：`DELETE /users/42`

### 📚 本週自學資源

| 資源 | 用途 |
|------|------|
| [Hoppscotch](https://hoppscotch.io/) | 線上 API 測試工具 |
| [JSONLint](https://jsonlint.com/) | 驗證 JSON 格式是否正確 |
| [HTTP Status Codes](https://httpstatuses.com/) | 所有狀態碼的完整說明 |
| [Public APIs List](https://github.com/public-apis/public-apis) | 數百個免費公開 API 清單 |
| [ExchangeRate-API 文件](https://www.exchangerate-api.com/docs/standard-requests) | 本週匯率 API 的完整文件 |

---

### 📝 下週預告

> 🔜 **Week 06：資料庫設計實戰 — Google Sheets 進階架構**
>
> - 為什麼你的試算表越做越亂？認識「正規化（Normalization）」
> - 多表關聯設計：主表、子表、外鍵的概念
> - VLOOKUP 進階：跨試算表查詢和動態範圍
> - 資料清洗技巧：TRIM、CLEAN、TEXT 函數組合
> - 把你的實習申請試算表重構成正規化的多表架構
>
> **課前任務：** 打開你在 Week 02 建立的實習媒合試算表，
> 觀察看看：有哪些資料是重複存在多個欄位的？
> 哪些資料應該獨立成一張表？

---

*雲端與數位內容管理 ｜ 國立金門大學 工業工程與管理學系 ｜ 2026 春季學期*
*Week 05 of 14*
