# LINE 行事曆 AI 助理（LINE Bot + n8n + Gemini + Google Calendar）

用自然語言在 LINE 上管理 Google 行事曆。例如傳送「明天下午四點開會」，機器人會自動建立行程並回覆確認。

## 功能
- 以自然語言新增、查詢、修改、刪除 Google Calendar 行程
- AI Agent（Google Gemini）理解語意並呼叫行事曆工具
- Simple Memory 保留對話上下文
- 每日 07:50 排程，透過 LINE Push API 主動提醒

## 系統架構
```
LINE 使用者
   ↓ 訊息
LINE 平台 ──Webhook(HTTPS)──▶ ngrok 隧道 ──▶ 本機 Docker n8n
                                                │
                      ┌─────────────────────────┤
                      ▼                         ▼
              AI Agent (Gemini)        Google Calendar 工具
                      │
                      ▼
          HTTP Request → LINE Push API → 使用者
```

## 技術棧
| 元件 | 用途 |
|---|---|
| n8n 2.44.7 (Docker) | 工作流自動化引擎 |
| LINE Messaging API | 使用者介面與訊息收發 |
| Google Gemini | 語意理解（AI Agent 的 LLM） |
| Google Calendar API (OAuth2) | 行程讀寫 |
| ngrok | 內網穿透，提供公開 HTTPS Webhook |

## 專案結構
```
.
├── README.md
├── docker-compose.yml
├── .env.example
├── ngrok.example.yml
├── workflow/
│   └── line-calendar-bot.json   # n8n 匯出檔
└── docs/
    └── setup.md                 # 詳細安裝步驟與疑難排解
```

## 快速開始
詳見 [docs/setup.md](docs/setup.md)。

## 開發過程遇到的問題
- 容器未掛載 Volume 導致憑證遺失 → 改用具名 Volume 與 `--restart unless-stopped`
- LINE Webhook 需公開 HTTPS → 使用 ngrok 固定網域
- Google OAuth redirect_uri_mismatch、invalid_client、API 未啟用
- Gemini 舊模型下架（404）→ 改用新模型
- LINE 回覆 Body 因換行與引號造成 JSON 錯誤 → 以 `JSON.stringify` 組裝

## 安全性
本 repo 不包含任何 API Key、Token 或 OAuth Secret。憑證皆存放於 n8n 內部加密資料庫。

## 授權
MIT License（可自行調整）
