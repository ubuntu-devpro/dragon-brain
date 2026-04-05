# 聯絡人管理系統 - 系統設計文件

## 1. 系統架構圖

```
┌─────────────────────────────────────────────────────────┐
│                      Client (Browser)                   │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│   │  聯絡人列表頁  │  │  新增/編輯表單 │  │   搜尋功能    │  │
│   └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  │
│          │                 │                 │          │
│          └────────────┬────┴────────┬────────┘          │
│                       │             │                   │
│                    AJAX / Fetch API                      │
└───────────────────────┼─────────────┼───────────────────┘
                        │             │
              ┌─────────▼─────────────▼─────────┐
              │         Flask Backend            │
              │  ┌───────────────────────────┐  │
              │  │      REST API Layer        │  │
              │  │  /contacts (GET/POST)      │  │
              │  │  /contacts/<id> (GET/PUT/DELETE) │
              │  │  /contacts/search         │  │
              │  └───────────┬───────────────┘  │
              │              │                   │
              │  ┌───────────▼───────────────┐  │
              │  │    Business Logic Layer   │  │
              │  │  - Validation             │  │
              │  │  - Search/Filter          │  │
              │  │  - ID Generation          │  │
              │  └───────────┬───────────────┘  │
              │              │                   │
              │  ┌───────────▼───────────────┐  │
              │  │    Data Access Layer       │  │
              │  │    contacts.json (R/W)    │  │
              │  └───────────────────────────┘  │
              └─────────────────────────────────┘
```

### 前後端互動流程

1. **瀏覽器發送 HTTP 請求** → Flask 接收請求
2. **Flask 路由分發** → 對應的 API 端點處理
3. **商業邏輯處理** → 資料驗證、搜尋過濾
4. **資料存取** → 讀寫 `contacts.json`
5. **Flask 回傳 JSON 響應** → 前端 AJAX 處理
6. **前端更新 DOM** → 無刷新更新畫面

---

## 2. API 規格

### 2.1 取得所有聯絡人

```
GET /contacts
```

**響應 (200 OK)**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-001",
      "name": "王小明",
      "phone": "0912-345-678",
      "email": "wang@example.com",
      "company": "ABC 公司",
      "notes": "業務窗口",
      "created_at": "2026-04-05T10:00:00Z",
      "updated_at": "2026-04-05T10:00:00Z"
    }
  ],
  "total": 1
}
```

### 2.2 取得單一聯絡人

```
GET /contacts/<id>
```

**響應 (200 OK)**
```json
{
  "success": true,
  "data": {
    "id": "uuid-001",
    "name": "王小明",
    "phone": "0912-345-678",
    "email": "wang@example.com",
    "company": "ABC 公司",
    "notes": "業務窗口",
    "created_at": "2026-04-05T10:00:00Z",
    "updated_at": "2026-04-05T10:00:00Z"
  }
}
```

**響應 (404 Not Found)**
```json
{
  "success": false,
  "error": "Contact not found"
}
```

### 2.3 新增聯絡人

```
POST /contacts
```

**請求 body**
```json
{
  "name": "王小明",
  "phone": "0912-345-678",
  "email": "wang@example.com",
  "company": "ABC 公司",
  "notes": "業務窗口"
}
```

**響應 (201 Created)**
```json
{
  "success": true,
  "data": {
    "id": "uuid-002",
    "name": "王小明",
    "phone": "0912-345-678",
    "email": "wang@example.com",
    "company": "ABC 公司",
    "notes": "業務窗口",
    "created_at": "2026-04-05T11:00:00Z",
    "updated_at": "2026-04-05T11:00:00Z"
  }
}
```

**響應 (400 Bad Request)**
```json
{
  "success": false,
  "error": "Name is required"
}
```

### 2.4 更新聯絡人

```
PUT /contacts/<id>
```

**請求 body**
```json
{
  "name": "王小明",
  "phone": "0922-345-678",
  "email": "wang.new@example.com",
  "company": "ABC 公司",
  "notes": "主要窗口"
}
```

**響應 (200 OK)**
```json
{
  "success": true,
  "data": {
    "id": "uuid-001",
    "name": "王小明",
    "phone": "0922-345-678",
    "email": "wang.new@example.com",
    "company": "ABC 公司",
    "notes": "主要窗口",
    "created_at": "2026-04-05T10:00:00Z",
    "updated_at": "2026-04-05T12:00:00Z"
  }
}
```

### 2.5 刪除聯絡人

```
DELETE /contacts/<id>
```

**響應 (200 OK)**
```json
{
  "success": true,
  "message": "Contact deleted"
}
```

### 2.6 搜尋聯絡人

```
GET /contacts/search?q=<keyword>
```

**Query Parameters:**
- `q` (必填): 搜尋關鍵字

**響應 (200 OK)**
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-001",
      "name": "王小明",
      "phone": "0912-345-678",
      "email": "wang@example.com",
      "company": "ABC 公司",
      "notes": "業務窗口",
      "created_at": "2026-04-05T10:00:00Z",
      "updated_at": "2026-04-05T10:00:00Z"
    }
  ],
  "total": 1,
  "query": "小明"
}
```

**搜尋範圍:** name, phone, email, company, notes（不區分大小寫）

---

## 3. 資料儲存

### contacts.json 檔案結構

```json
{
  "contacts": [
    {
      "id": "uuid-001",
      "name": "王小明",
      "phone": "0912-345-678",
      "email": "wang@example.com",
      "company": "ABC 公司",
      "notes": "業務窗口",
      "created_at": "2026-04-05T10:00:00Z",
      "updated_at": "2026-04-05T10:00:00Z"
    },
    {
      "id": "uuid-002",
      "name": "李小華",
      "phone": "0987-654-321",
      "email": "lee@example.com",
      "company": "XYZ 企業",
      "notes": "技術主管",
      "created_at": "2026-04-04T09:00:00Z",
      "updated_at": "2026-04-04T09:00:00Z"
    }
  ]
}
```

### 欄位說明

| 欄位 | 類型 | 必填 | 說明 |
|------|------|------|------|
| id | string | 是 | UUID，唯一識別碼 |
| name | string | 是 | 姓名 |
| phone | string | 否 | 電話號碼 |
| email | string | 否 | 電子郵件 |
| company | string | 否 | 公司名稱 |
| notes | string | 否 | 備註 |
| created_at | string | 是 | ISO 8601 時間格式，建立時間 |
| updated_at | string | 是 | ISO 8601 時間格式，更新時間 |

---

## 4. 前端頁面規劃

### 4.1 頁面結構

```
/                    → 聯絡人列表頁 (首頁)
/contacts/new        → 新增聯絡人表單
/contacts/<id>/edit  → 編輯聯絡人表單
```

### 4.2 頁面詳情

#### 4.2.1 聯絡人列表頁 (/)

**功能:**
- 顯示所有聯絡人卡片清單
- 搜尋列（即時過濾）
- 新增聯絡人按鈕
- 每筆資料的操作按鈕（檢視、編輯、刪除）

**UI 元件:**
```
┌─────────────────────────────────────────┐
│  🔍 搜尋聯絡人...                    [+ 新增] │
├─────────────────────────────────────────┤
│  ┌───────────────────────────────────┐  │
│  │ 👤 王小明                            │  │
│  │ 📞 0912-345-678                   │  │
│  │ ✉️  wang@example.com              │  │
│  │ 🏢 ABC 公司                         │  │
│  │                    [編輯] [刪除]   │  │
│  └───────────────────────────────────┘  │
│  ┌───────────────────────────────────┐  │
│  │ 👤 李小華                            │  │
│  │ 📞 0987-654-321                   │  │
│  │ ✉️  lee@example.com                │  │
│  │ 🏢 XYZ 企業                         │  │
│  │                    [編輯] [刪除]   │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

#### 4.2.2 新增聯絡人表單 (/contacts/new)

**功能:**
- 填寫聯絡人資訊表單
- 表單驗證（必填欄位檢查）
- 提交後返回列表頁

**UI 元件:**
```
┌─────────────────────────────────────────┐
│  ← 返回列表         新增聯絡人           │
├─────────────────────────────────────────┤
│  姓名 *      [____________________]     │
│  電話       [____________________]     │
│  電子郵件   [____________________]     │
│  公司       [____________________]     │
│  備註       [                    ]     │
│             [                    ]     │
│                                         │
│              [取消]  [儲存]              │
└─────────────────────────────────────────┘
```

#### 4.2.3 編輯聯絡人表單 (/contacts/<id>/edit)

**功能:**
- 預載入現有聯絡人資料
- 修改後提交更新
- 刪除確認對話框

**UI 元件:**
```
┌─────────────────────────────────────────┐
│  ← 返回列表         編輯聯絡人           │
├─────────────────────────────────────────┤
│  姓名 *      [王小明____________]       │
│  電話       [0912-345-678_____]        │
│  電子郵件   [wang@example.com__]       │
│  公司       [ABC 公司_________]        │
│  備註       [業務窗口_________]        │
│                                         │
│     [刪除此聯絡人]     [取消]  [更新]    │
└─────────────────────────────────────────┘
```

---

## 5. 技術實作概覽

### 5.1 專案目錄結構

```
contact-manager/
├── app.py              # Flask 主程式
├── data/
│   └── contacts.json  # 資料儲存檔案
├── templates/
│   ├── index.html      # 列表頁
│   ├── contact_form.html  # 新增/編輯表單（共用）
│   └── layout.html     # 基礎模板
├── static/
│   ├── css/
│   │   └── style.css   # 樣式表
│   └── js/
│       └── app.js      # 前端 JavaScript
├── requirements.txt    # Python 依賴
└── README.md
```

### 5.2 Flask 路由設計

| 端點 | 方法 | 說明 |
|------|------|------|
| `/` | GET | 列表頁 |
| `/contacts` | GET | API：取得所有聯絡人 |
| `/contacts` | POST | API：新增聯絡人 |
| `/contacts/<id>` | GET | API：取得單一聯絡人 |
| `/contacts/<id>` | PUT | API：更新聯絡人 |
| `/contacts/<id>` | DELETE | API：刪除聯絡人 |
| `/contacts/search` | GET | API：搜尋聯絡人 |
| `/contacts/new` | GET | 新增表單頁 |
| `/contacts/<id>/edit` | GET | 編輯表單頁 |

### 5.3 前端技術

- **HTML5** + **CSS3** (響應式設計)
- **Vanilla JavaScript** (無框架，依賴 Fetch API)
- **Jinja2 模板** (Flask 內建)

---

## 6. 錯誤處理

### HTTP 狀態碼

| 狀態碼 | 說明 |
|--------|------|
| 200 | 成功 |
| 201 | 建立成功 |
| 400 | 請求參數錯誤 |
| 404 | 資源不存在 |
| 500 | 伺服器內部錯誤 |

### 前端錯誤處理

- 表單驗證錯誤：即時顯示於表單欄位下方
- API 錯誤：Toast 通知或 Alert 訊息
- 網路錯誤：顯示重試選項

---

*文件版本: 1.0*
*建立日期: 2026-04-05*
