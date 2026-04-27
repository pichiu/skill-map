# Stage 1 偵察報告

## 1. 專案基本資訊

- **名稱**: 程序員技能圖譜 (StuQ Skill Map)
- **維護組織**: 極客邦科技 Geekbang（https://www.geekbang.org/）由 StuQ 發起
- **GitHub**: https://github.com/TeamStuQ/skill-map（19.4k stars，4.4k forks）
- **授權**: CC-BY-NC-SA 4.0
- **版本狀態**: `index.html` 標示 v0.1.2，`README.md` 提及 V1.0（不一致，詳見落差清單）

## 2. 專案性質

**靜態前端單頁網站（Pure Frontend SPA）**，無後端服務器。

- 社群驅動的 IT 技術技能圖譜平台
- 目標受眾：各類別程式設計師（AI、前端、後端、雲端、大數據、安全、移動端、DevOps 等）
- 核心價值：彙整各領域專家貢獻的學習路徑 Markdown 文件，以樹狀可折疊介面呈現

## 3. 目錄結構（3層）

```
skill-map/
├── index.html            # 唯一入口頁面，靜態 HTML
├── package.json          # Node.js 設定（devDependencies 只有 Gulp 工具鏈）
├── gulpfile.js           # 建置腳本（SASS 編譯 + GitHub Pages 部署）
├── test.md               # 測試用 Markdown 文件（無自動化測試框架）
├── .gitignore            # 排除 node_modules, .sass-cache, .publish 等
│
├── js/
│   ├── script.js         # 應用邏輯（約 215 行，Vanilla JS）
│   └── marked.min.js     # Markdown→HTML 解析器（第三方 library，bundle 在 repo 中）
│
├── css/
│   ├── style.css         # 編譯後的 CSS（由 Gulp+SASS 產生）
│   └── style.css.map     # Source map
│
├── sass/
│   ├── style.sass        # 主要 SASS 樣式
│   └── _base.sass        # 基礎樣式變數/mixin
│
├── data/
│   ├── map-*.md          # 40+ 個技能圖譜 Markdown 文件（各技術領域）
│   ├── designbyStuQ/     # 各圖譜的 PNG 圖片版本（18 個）
│   ├── Preview-source-skillmap-PNG.md  # PNG 圖片索引
│   └── operation-command.png           # 運維命令參考圖
│
├── img/
│   ├── plus.png / minus.png  # 樹節點展開/折疊圖示
│   ├── top.svg               # 回頂部按鈕圖示
│   ├── qq.png / wechat.png   # 社群 QR Code 圖片
│   └── GeekTime-QRCode-*.png # 極客時間 App QR Code
│
└── xmind/
    └── *.xmind           # 11 個 XMind 格式技能圖譜（與 data/ 的 .md 對應）
```

**架構模式**: 靜態網站（Static Website），無框架，純 Vanilla JavaScript

## 4. 技術棧

| 類別 | 技術 | 版本 | 備註 |
|------|------|------|------|
| 語言 | HTML5 | - | 唯一入口：index.html |
| 語言 | CSS3 / SASS | - | style.sass → style.css（Gulp 編譯） |
| 語言 | JavaScript (ES5) | - | Vanilla JS，無 transpiler |
| Library | marked.js | bundled | Markdown→HTML 解析，直接 bundle 在 repo |
| 建置工具 | Gulp | ^3.9.0 | SASS 編譯 + GitHub Pages 部署 |
| Gulp Plugin | gulp-sass | ^2.1.1 | SASS 編譯 |
| Gulp Plugin | gulp-sourcemaps | ^1.6.0 | Source map 生成 |
| Gulp Plugin | gulp-gh-pages | ^0.5.4 | 部署到 GitHub Pages |
| 開發伺服器 | http-server | ^0.8.5 | 本地靜態伺服器（port 9999） |
| 資料格式 | Markdown | - | 技能圖譜原始資料（data/*.md） |
| 資料格式 | XMind | - | 技能圖譜的心智圖格式（xmind/*.xmind） |
| 分析 | 百度統計 (Baidu Analytics) | - | index.html 中嵌入（hm.baidu.com） |
| 授權 | CC-BY-NC-SA 4.0 | - | 內容授權 |

## 5. 既有文件掃描

### 文件位置
- `README.md`（根目錄）— 主要文件，包含：領域清單、貢獻者、Markdown 分級規範、版本歷史
- `data/Preview-source-skillmap-PNG.md` — PNG 圖片索引
- `test.md` — 測試用 Markdown 草稿，非正式文件

### README.md 關鍵資訊摘要
- Markdown 分級規範：`#,##,###` 為高階分支，`-,*,+` 為次階分支（`-` > `*` > `+`）
- 涵蓋 30+ 技術領域，共 40+ 個 skill map Markdown 文件
- 開放貢獻：PR / Issue / Email (sean@geekbang.org)

## 6. 落差清單（文件 vs 程式碼）

| # | 文件說 | 程式碼實際 | 位置 |
|---|--------|-----------|------|
| 1 | README 提及 V1.0 版本 | index.html 標題仍顯示 "v0.1.2" | `index.html:22` |
| 2 | 多個技能圖譜已在 data/ 添加 | index.html 只顯示 7 個領域（Web前端/雲計算/安全/智能運維/大數據/智能硬件/開發語言），新增的 30+ 圖譜未整合進網頁 | `index.html:80-115` |
| 3 | script.js 載入 `data/frontEnd.md` | 實際檔案是 `data/map-FrontEndEngineer.md` | `js/script.js:127` |
| 4 | script.js 載入 `data/big-data.md` | 實際檔案是 `data/map-BigDataEngineer.md` | `js/script.js:128` |
| 5 | script.js 載入 `data/cloudComputing.md` | 實際 `data/map-CloudComputing.md` | `js/script.js:129` |
| 6 | script.js 載入 `data/dev-lang.md` | 無此檔案；最接近是 `data/map-DevLang-Total.md` | `js/script.js:127` |
| 7 | script.js 載入 `data/IH.md` | 無此檔案；最接近是 `data/map-EmbeddedEngineer.md` | `js/script.js:131` |
| 8 | script.js 載入 `data/IOAM.md` | 無此檔案；最接近是 `data/map-IntelligentDevOps.md` | `js/script.js:132` |
| 9 | script.js 載入 `data/security.md` | 無此檔案；最接近是 `data/map-SecurityEngineer.md` | `js/script.js:133` |
| 10 | index.html 提供下載連結 `data/frontEnd.md` | 檔案不存在，下載連結失效 | `index.html:82` |

**⚠️ 嚴重問題**: 落差 3-10 意味著網站在標準 `npm start` 啟動後，所有技能圖譜的 AJAX 請求都會 404 失敗，頁面技能樹無法顯示。這表明此 repo 的 `data/` 結構在某個時間點被重構（檔案改名），但 `index.html` 和 `script.js` 未同步更新。

## 7. 技能圖譜資料文件清單

| 文件 | 領域 |
|------|------|
| map-MachineLearning.md | 機器學習 |
| map-Apollo.md | Apollo 自動駕駛 |
| map-BigDataEngineer.md | 大數據 |
| map-Hadoop.md | Hadoop |
| map-FrontEndEngineer.md | Web 前端 |
| map-MobilePerformanceOptimization.md | 移動性能優化 |
| map-HTML5.md | HTML5 開發 |
| map-AngularJS2.md | Angular 2 |
| map-Architect.md | 架構師 |
| map-OpenResty.md | OpenResty |
| map-LiveTelecast.md | 直播技術 |
| map-CDN.md | CDN 技術 |
| map-dns-troubleshoot.md | DNS 排查 |
| map-CloudComputing.md | 雲計算 |
| map-Container.md | 容器 |
| map-Serverless.md | Serverless |
| map-Microservice.md | 微服務 |
| map-SecurityEngineer.md | 安全工程師 |
| map-IntelligentDevOps.md | 智能運維 |
| map-DBA.md | DBA |
| map-DevOps.md | DevOps |
| map-Kubernetes.md | Kubernetes |
| map-testing.md | 測試 |
| map-MobileWirelessTesting.md | 移動無線測試 |
| map-MobileDev-iOSDev.md | iOS 開發 |
| map-MobileDev-AndroidDev.md | Android 開發 |
| map-MobileDev-AndroidROMDev.md | Android ROM 開發 |
| map-MobileDev-AndroidArchitect.md | Android 架構師 |
| map-EmbeddedEngineer.md | 嵌入式開發 |
| map-DevLang-Total.md | 開發語言總覽 |
| map-DevLang-Golang.md | Golang |
| map-DevLang-Clojure.md | Clojure |
| map-DevLang-Python.md | Python |
| map-DevLang-Haskell.md | Haskell |
| map-DevLang-Nodejs.md | Node.js |
| map-DevLang-Ruby.md | Ruby |
| map-DevLang-Java.md | Java |
| map-DevLang-PHP.md | PHP |
| map-Git.md | Git |
| map-CTO.md | CTO 技能 |
