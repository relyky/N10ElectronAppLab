# N10ElectronAppLab

這是一個 Electron App 練習專案，依照 [Microsoft Learn〈winapp CLI – Electron 設定〉指南](https://learn.microsoft.com/zh-tw/windows/apps/dev-tools/winapp-cli/guides/electron-setup)建立，目標是在 Electron 中呼叫 Windows API。
- 技術棧：Electron 45 + Electron Forge 8（template `base`，不用 bundler，使用 JavaScript），以及 `@microsoft/winappcli`
- 程式碼放在 repo 根目錄；開發 IDE 用 VS Code
- 計劃與進度：`docs/electron-app-setup-plan.md`、`docs/electron-app-setup-tasks.md`

## 決策紀錄

### 2026-11-18：暫不開啟開發人員模式、不使用偵錯身份
- 目前**不**開啟 Windows 開發人員模式，也**不**執行 `winapp node add-electron-debug-identity`。
- 影響如下：
  - App 用 `npm start` 啟動時不帶套件身份，所以需要 App 身份的 Windows API（例如通知）無法使用。一般 UI 開發不受影響。
  - `package.json` 的 `postinstall` 裡仍有 `add-electron-debug-identity`，所以 `npm install` 結尾會出現 `Developer Mode is not enabled`（0x80131509）。這是**預期中的錯誤**；前面的 `install-electron` 和 `winapp restore` 都已正常完成。
- 不要主動提議開啟開發人員模式或補建偵錯身份；等使用者改變決定再處理。

### 2026-11-18：VS NativeDesktop 工作負載延後安裝
- 等到需要 native addon（`winapp node create-addon`）時才安裝。
