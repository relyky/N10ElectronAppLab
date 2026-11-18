# Tasks：依 winapp CLI 指南建立 Electron App

> 對應計劃：[electron-app-setup-plan.md](./electron-app-setup-plan.md)
> 參考文件：<https://learn.microsoft.com/zh-tw/windows/apps/dev-tools/winapp-cli/guides/electron-setup>
> 最後更新：2026-11-18

圖例：`[x]` 完成 `[ ]` 待辦 `[!]` 卡關

## 先決條件
- [x] Windows 11
- [x] Node.js ≥ 22.13（實際 v24.18.0）
- [x] .NET SDK 10（實際 10.0.401）
- [ ] Visual Studio NativeDesktop 工作負載（**延後**，做 native addon 時再裝；開發 IDE 用 VS Code）
- [ ] Windows 開發人員模式（**決定暫不開啟**，見 CLAUDE.md 決策紀錄）

## Step 1：建立 Electron App
- [x] `npm create electron-app@latest my-windows-app -- --template=base --skip-git`（不用 bundler、JavaScript、Electron 最新版 45.3.4）
- [x] `npx --no-install install-electron` 下載 Electron binary（Electron 42 以上版本必須做）
- [x] `npm start` 能開出預設視窗

## Step 2：安裝 winapp CLI
- [x] `npm install --save-dev @microsoft/winappcli`（0.8.0）

## Step 3：winapp init
- [x] `npx winapp init . --use-defaults --add-js-bindings`
- [x] 確認產生了 `.winapp/`（已加入 .gitignore）、`Package.appxmanifest`、`Assets/`、`winapp.yaml`、JS bindings（`#winapp/bindings`）

## Step 4：在 postinstall 加入 restore
- [x] `package.json` 加入適用 Electron 42 以上版本的 postinstall：`npx --no-install install-electron && winapp restore && winapp node add-electron-debug-identity`
- [!] 執行 `npm install`：restore 成功，但 `add-electron-debug-identity` 失敗（0x80131509，開發人員模式沒開）

## Step 5：偵錯身份與驗證
- [ ] ~~開好開發人員模式後重跑 `npm install`~~（**暫緩**：決定暫不使用偵錯身份）
- [ ] ~~`npm start` 確認是帶偵錯身份的桌面 App 視窗~~（**暫緩**）
- [x] `npm start` 能開出不帶身份的桌面 App 視窗
- [ ] 如果出現當機或白畫面，把 start 改成 `electron-forge start -- --no-sandbox`

## 結構調整
- [x] 把 `my-windows-app/` 搬到 repo 根目錄
- [x] 刪掉含舊絕對路徑的 `.winapp/`，用 `npm install` 重建
- [x] 搬移後 `npm start` 正常
- [x] 更新 `electron-app-setup-plan.md`

## 收尾
- [ ] `git status` 確認只會 commit 原始檔（目前排除項目都正確）
- [ ] Commit（等使用者指示）
- [ ] work-diary 記工時表
