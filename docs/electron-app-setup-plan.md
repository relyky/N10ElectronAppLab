# 依 winapp CLI 指南建立 Electron App

> 工作進度：[electron-app-setup-tasks.md](./electron-app-setup-tasks.md)

## Context
使用者要照 [Microsoft Learn〈winapp CLI – Electron 設定〉指南](https://learn.microsoft.com/zh-tw/windows/apps/dev-tools/winapp-cli/guides/electron-setup) Step 1–5，在空的 repo `D:\GitHubRepos\N10ElectronAppLab` 建立一個能呼叫 Windows API 的 Electron App。App 放在 repo 根目錄，開發 IDE 用 VS Code。

> **2026-11-18 更新**：App 原本用 `npm create electron-app my-windows-app` 建在子資料夾 `my-windows-app/`，之後整個搬到 repo 根目錄。搬完後刪掉 `.winapp/`，再用 `npm install`（postinstall 裡的 `winapp restore`）重建，因為 `.winapp/include/.cppwinrt.rsp` 寫死了舊的絕對路徑。套件名稱和 manifest identity 仍是 `my-windows-app`。
>
> **實際情況**：Electron 是 45.3.4，所以用了適用 42 以上版本的 postinstall。`winapp init` **沒有**自動開啟開發人員模式，結果 `add-electron-debug-identity` 失敗（0x80131509）。需要使用者手動開啟開發人員模式後，再執行一次 `npm install`。

環境檢查：Node v24.18.0（指南要求 ≥22.13）✅、.NET SDK 10.0.401 ✅、VS 2022/2026 Community 都**沒有** NativeDesktop 工作負載（使用者決定先跳過，之後做 native addon 時再裝）、Windows 開發人員模式未開啟（`winapp init` 會嘗試開啟，可能跳 UAC，需要使用者按同意）。

## 步驟

### Step 1：建立 Electron Forge App
- 在 repo 根目錄執行 `npm create electron-app@latest my-windows-app`，用非互動方式選：bundler 不用（template `base`）、語言 JavaScript、Electron 最新版、**跳過 git init**（沿用外層 repo）。
  - 先試 `npm create electron-app@latest my-windows-app -- --template=base --skip-git`。如果 CLI 還是會互動提問或不接受這些 flag，就依實際 `--help` 調整。
- 用背景模式跑一次 `npm start`，確認 Forge 預設視窗能開，然後關掉。

### Step 2：安裝 winapp CLI
- 在 `my-windows-app/` 執行 `npm install --save-dev @microsoft/winappcli`

### Step 3：winapp init
- 執行 `npx winapp init . --use-defaults --add-js-bindings`（等同互動模式全部按 Enter、Stable SDK、加入 JS bindings）。
- 確認有產生 `.winapp/`（已加入 .gitignore）、`Package.appxmanifest`、`Assets/`、`winapp.yaml`、`package.json` 裡的 `winapp.jsBindings`，以及 `@microsoft/dynwinrt*` 相依套件。

### Step 4：在 postinstall 加入 restore
- 先看 `node_modules/electron/package.json` 的版本：
  - ≥42：`"postinstall": "npx --no-install install-electron && winapp restore && winapp node add-electron-debug-identity"`
  - <42：`"postinstall": "winapp restore && winapp node add-electron-debug-identity"`
- 不採用跨平台的 `scripts/postinstall.js`，因為只在 Windows 開發。
- 執行 `npm install` 觸發 postinstall。

### Step 5：偵錯身份與驗證
- 執行 `npm start`，確認是桌面 App 視窗。
- 如果出現已知問題（啟動時當機或白畫面），把 start script 改成 `electron-forge start -- --no-sandbox`。

### 收尾
- 確認 `.gitignore` 有排除 `node_modules/`、`.winapp/`、`out/`。不 commit，等使用者要求。
- 用 work-diary skill 記一列工時表。

## 驗證
- Step 1 和 Step 5 的 `npm start` 都能開出 Electron 視窗（看 console 輸出，必要時請使用者目視確認）。
- `npx winapp node add-electron-debug-identity` 執行成功沒有錯誤。
- `git status` 只看到 `my-windows-app/` 的原始檔，沒有 `node_modules`、`.winapp`。
