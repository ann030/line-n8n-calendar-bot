# Workflow 匯出檔

請將 n8n 匯出的 `line-calendar-bot.json` 放在此資料夾。

匯出方式：n8n 開啟工作流 → 右上角 `...` → Download。

上傳前請檢查：
- 不含 API Key、Channel Access Token（n8n 匯出預設不含憑證內容）
- 若 HTTP Request 的 Body 寫死了自己的 LINE userId，請改為 `YOUR_LINE_USER_ID`
- 匯入後需在 n8n 重新綁定 Google Calendar、Gemini、Header Auth 三個憑證
