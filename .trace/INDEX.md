# StuQ 程序員技能圖譜 — 專案總覽

## 一句話摘要

**程序員技能圖譜**是由極客邦科技（Geekbang）發起的社群開源專案，彙整 30+ 技術領域的學習路徑 Markdown 文件，並透過一個純前端靜態網站以可折疊樹狀介面呈現，幫助各類程式設計師梳理知識框架。

---

## 技術棧總覽

| 類別 | 技術 | 版本 | 用途 |
|------|------|------|------|
| 入口 | HTML5 | - | 唯一頁面：`index.html` |
| 樣式 | CSS3 / SASS | - | `sass/style.sass` → `css/style.css` |
| 語言 | JavaScript (ES5) | - | 純 Vanilla JS，無框架 |
| 解析器 | marked.js | bundled | Markdown → HTML 轉換 |
| 建置 | Gulp | ^3.9.0 | SASS 編譯 + GitHub Pages 部署 |
| Gulp Plugin | gulp-sass | ^2.1.1 | SASS 編譯 |
| Gulp Plugin | gulp-sourcemaps | ^1.6.0 | Source map |
| Gulp Plugin | gulp-gh-pages | ^0.5.4 | 部署至 GitHub Pages |
| 開發伺服器 | http-server | ^0.8.5 | 靜態文件服務（port 9999） |
| 資料格式 | Markdown | - | 技能圖譜原始資料（`data/map-*.md`） |
| 資料格式 | XMind | - | 心智圖格式（`xmind/*.xmind`） |
| 分析 | 百度統計 | - | 頁面流量追蹤 |
| 授權 | CC-BY-NC-SA 4.0 | - | 內容授權 |

---

## 關鍵指令速查

```bash
# 安裝依賴
npm install

# 啟動本地開發伺服器（port 9999，自動開啟瀏覽器）
npm start

# 編譯 SASS → CSS
npm run gulp
# 或直接：
npx gulp sass

# 部署到 GitHub Pages
npx gulp deploy
```

> ⚠️ **注意**: Gulp 3.x + gulp-sass 2.x 對新版 Node.js 不相容，建議使用 Node.js 10-12。

---

## 文件地圖

| 文件 | 內容 |
|------|------|
| [INDEX.md](./INDEX.md) | 本文件：專案總覽與速查 |
| [CODEBASE_MAP.md](./CODEBASE_MAP.md) | 程式碼地圖：目錄說明、「我想改 X 看哪裡」速查表 |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | 系統架構：元件關係、流程 Sequence Diagram |
| [DATA_MODEL.md](./DATA_MODEL.md) | 資料模型：Markdown 格式規範、檔案結構 |
| [API_SURFACE.md](./API_SURFACE.md) | 介面參考：JavaScript 公開介面、DOM 介面 |
| [DEV_GUIDE.md](./DEV_GUIDE.md) | 開發者上手指南：環境建置、貢獻流程 |
| [DISCOVERY_LOG.md](./DISCOVERY_LOG.md) | 探索紀錄：發現的問題、技術債、待解疑問 |

---

## ⚠️ 重要已知問題

**網站目前無法正常顯示技能圖譜內容**：`js/script.js` 中引用的 Markdown 文件路徑（如 `data/frontEnd.md`）與 `data/` 目錄中實際存在的檔案名稱（如 `data/map-FrontEndEngineer.md`）不符，導致所有 AJAX 請求返回 404。詳見 [DISCOVERY_LOG.md](./DISCOVERY_LOG.md)。

---

## 專案術語表

| 術語 | 說明 |
|------|------|
| Skill Map / 技能圖譜 | 某技術領域的學習路徑樹狀圖，以 Markdown 格式撰寫 |
| StuQ | 極客邦科技旗下的開發者學習平台（已整合至 Geekbang） |
| Geekbang / 極客邦科技 | 本專案的發起組織（https://www.geekbang.org/） |
| map-*.md | `data/` 目錄下的技能圖譜 Markdown 文件（命名規範：`map-{領域名}.md`） |
| mdList | `script.js` 中的 DOM id → Markdown 路徑映射物件 |
| 葉節點 / Leaf Node | 樹中沒有子 `<li>` 的節點，顯示為不可折疊 |
| opened / closed / normal | `<li>` 元素的三種 CSS class 狀態（展開/折疊/葉節點） |
| Revealing Module Pattern | `skillmap` 模組採用的 JavaScript 設計模式 |
| XMind | 心智圖軟體，`xmind/` 目錄下存有各技能圖譜的 `.xmind` 格式版本 |
| gulp deploy | 將網站部署到 GitHub Pages 的 Gulp 任務 |
