# 安裝與設定步驟

## 1. 啟動 n8n
```bash
cp .env.example .env     # 填入自己的 ngrok 網域
docker compose up -d
```
開啟 http://localhost:5678 建立 Owner 帳號。

## 2. 啟動 ngrok 隧道
```bash
ngrok config add-authtoken <YOUR_TOKEN>
ngrok http --domain=<your-domain>.ngrok-free.dev 5678
```
開機自動啟動（系統管理員 CMD）：
```bash
ngrok service install --config "<ngrok.yml 路徑>"
ngrok service start
```

## 3. 建立憑證（n8n → Credentials）
| 憑證 | 內容 |
|---|---|
| Google Calendar OAuth2 | Client ID / Secret 來自 Google Cloud；Redirect URI 設為 `https://<ngrok網域>/rest/oauth2-credential/callback`；需啟用 Google Calendar API |
| Google Gemini (PaLM) API | 來自 aistudio.google.com 的 API Key |
| Header Auth | Name: `Authorization`，Value: `Bearer <LINE Channel Access Token>` |

## 4. 匯入 workflow 並發佈
1. Import `workflow/line-calendar-bot.json`
2. 各節點重新選擇上述憑證，Calendar 欄位選自己的行事曆
3. 按 Publish

## 5. 設定 LINE
LINE Developers → Messaging API：
- Webhook URL：`https://<ngrok網域>/webhook/<Webhook節點路徑>`
- 開啟 Use webhook，按 Verify
- 關閉「自動回應訊息」

## 疑難排解
| 症狀 | 原因與解法 |
|---|---|
| Verify 404 | 工作流未 Publish，或使用了 webhook-test 網址 |
| redirect_uri_mismatch | Google Cloud 的 Redirect URI 與 n8n 顯示的不一致 |
| invalid_client | Client ID / Secret 貼錯或不是同一組 |
| 403 Calendar API not used | 在 Google Cloud 啟用 Google Calendar API |
| resource not found（Gemini） | 模型已下架，改選較新的模型 |
| Authorization failed（LINE） | Header Auth 的 Bearer Token 錯誤 |
| Bad request（LINE） | Body JSON 格式錯誤，改用 JSON.stringify 組裝 |
| ERR_NGROK_121 | ngrok 版本過舊，執行 ngrok update |
