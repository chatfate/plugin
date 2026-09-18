# ChatFate

[简体中文](README.md) · [English](README.en.md) · **繁體中文**

在熟悉的對話裡，問一件事、認識自己。ChatFate 是由 ChatFate 團隊維護的八字與六爻外掛，透過遠端服務完成排盤、解讀和報告。

[造訪官網](https://chatfate.cc) · [安裝說明](https://chatfate.cc/install.md)

## 可以做什麼

- **今日指引**：看看今天值得留意的事。
- **問一件事**：圍繞具體問題進行六爻解讀。
- **年度主題**：了解一年的主題和不同階段。
- **人生命盤**：透過八字解讀認識自己。
- 登入 Google 帳號後，儲存並回顧自己的報告。

## 安裝與更新

以下安裝方式適用於 macOS 上的 ChatGPT／Codex 桌面應用程式，需要能夠使用 Codex 外掛的環境；不是把指令貼到任意聊天網頁就能安裝。

把下面這句話傳送到 Codex：

> 閱讀 https://chatfate.cc/install.md，幫我在 Codex 中安裝 ChatFate 外掛；安裝完成後建立一個新任務並開始使用 ChatFate。

也可以在終端機執行官網安裝程式：

```sh
curl -fsSL https://chatfate.cc/i | sh
```

安裝程式使用桌面應用程式內建的 Codex CLI，從本儲存庫的 `main` 分支安裝或更新 ChatFate，並確認官方 Browser 外掛已啟用。舊版安裝也使用同一個入口更新。

安裝後，在新任務中選擇 `@ChatFate`，傳送「開始使用 ChatFate」。不要在已經執行中的舊任務裡判斷新外掛是否載入成功。

如安裝失敗，請保留錯誤訊息和外掛版本，透過本儲存庫的 Issues 回報；不要公開 API Key、登入憑證或私人報告連結。

## 這個儲存庫包含什麼

這裡是供安裝使用的公開外掛套件，包含外掛宣告、遠端 MCP 連線設定、Skills 和品牌資源。排盤引擎、網站、帳號、付款、資料庫及部署原始碼不在本儲存庫中。

維護人員在私有專案開發和測試，審核後只匯出公開外掛檔案，以 ChatFate 員工帳號發布。公開儲存庫僅保留 `main` 分支，每次正式發布記錄一個發布提交，不合入私有開發歷史。

## 使用說明

八字與六爻屬於傳統文化解讀，不保證現實預測結果，也不能取代醫療、法律或投資等專業建議。ChatGPT／Codex 的訂閱及模型使用費用，與 ChatFate 報告購買相互獨立。

[隱私權政策](https://chatfate.cc/privacy) · [使用條款](https://chatfate.cc/terms)
