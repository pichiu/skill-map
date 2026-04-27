# DISCOVERY_LOG.md — 探索紀錄與待解問題

> **專案**: 程序員技能圖譜（StuQ Skill Map）  
> **紀錄日期**: 2026-04-27  
> **紀錄者**: 自動化偵察腳本 + 技術文件分析  

---

## 1. Web Search 發現摘要

### 搜尋一：專案總覽（查詢：TeamStuQ skill-map GitHub programmer skill roadmap）

| 連結 | 關鍵 Takeaway |
|------|--------------|
| [GitHub - TeamStuQ/skill-map](https://github.com/TeamStuQ/skill-map) | 主倉庫：19.4k stars，4.4k forks，極客邦科技（Geekbang）發起 |
| [SourceForge mirror](https://sourceforge.net/projects/skill-map.mirror/) | 第三方鏡像存在，說明社群接受度高 |
| [GitHub Issues](https://github.com/TeamStuQ/skill-map/issues) | 社群透過 Issue 持續推薦學習路徑與資源 |

**關鍵 Takeaway**：
- 這是一個**文件型開源專案**（Documentation-centric Open Source Project），核心價值在於社群共建的技能圖譜內容，而非技術複雜度
- 已有 4.4k forks，代表大量開發者以此為基礎建立自己的版本
- 技術實作相對簡單，Markdown 內容才是主體；任何技術債的修復均不影響核心價值

### 搜尋二：技術架構確認（查詢：StuQ Geekbang skill-map marked.js static site architecture）

| 連結 | 關鍵 Takeaway |
|------|--------------|
| [GitHub index.html](https://github.com/TeamStuQ/skill-map/blob/master/index.html) | 確認為純靜態 HTML 單頁架構，無後端 |
| [jamsonwoo/StuQ_skill-map](https://github.com/jamsonwoo/StuQ_skill-map) | 原創者之一自己的 fork，部分功能演進發生在 fork 中 |
| [http://skill-map.stuq.org/](http://skill-map.stuq.org/) | 官方網站（當前狀態未驗證，對應 GitHub Pages 透過 `gulp deploy` 部署） |

**相關資源**：
- 主倉庫：https://github.com/TeamStuQ/skill-map
- 官方網站：http://skill-map.stuq.org/（http://www.stuq.org/subject/skill-map/）
- 問題回報：https://github.com/TeamStuQ/skill-map/issues
- 聯絡：sean@geekbang.org
- 授權：CC-BY-NC-SA 4.0

---

## 2. 程式碼 vs 文件落差清單

> 來源：`recon.md` 落差清單章節  
> **整體評估**：`data/` 目錄在某時間點被重構（檔案集體改名加上 `map-` 前綴），但 `index.html` 與 `script.js` 未同步更新，導致網站核心功能完全失效。

---

### 落差 1：版本號不一致

| 項目 | 內容 |
|------|------|
| **問題描述** | README.md 提及 V1.0 版本，但 `index.html:22` 的頁面標題仍顯示 "v0.1.2" |
| **影響** | 使用者看到的版本號與文件說明不符，造成混淆；輕微 |
| **建議修復** | 統一版本號：將 `index.html` 標題中的 "v0.1.2" 更新為 "V1.0" 或移除版本號 |

---

### 落差 2：網頁展示內容嚴重落後於資料現況

| 項目 | 內容 |
|------|------|
| **問題描述** | `data/` 目錄已有 40+ 個技能圖譜 Markdown 文件，但 `index.html:80-115` 只展示 7 個領域（Web 前端、雲計算、安全、智能運維、大數據、智能硬件、開發語言） |
| **影響** | 30+ 個新增圖譜（機器學習、DevOps、容器、Kubernetes、微服務、DBA、測試、iOS/Android、Golang、Python 等）完全未整合進可互動的網頁介面 |
| **建議修復** | 在 `index.html` 的 `<ul id="rootList">` 中為每個 `data/map-*.md` 補充對應 `<li>` 區塊，並同步更新 `js/script.js` 的 `mdList` 物件 |

---

### 落差 3：`script.js` 載入路徑錯誤 — frontEnd

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:127` 載入 `data/frontEnd.md`，實際檔案為 `data/map-FrontEndEngineer.md` |
| **影響** | XHR 請求收到 404，Web 前端技能樹無法顯示（顯示為空） |
| **建議修復** | 將路徑改為 `data/map-FrontEndEngineer.md`（見第 3 節完整對照表） |

---

### 落差 4：`script.js` 載入路徑錯誤 — bigData

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:128` 載入 `data/big-data.md`，實際檔案為 `data/map-BigDataEngineer.md` |
| **影響** | 大數據技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-BigDataEngineer.md` |

---

### 落差 5：`script.js` 載入路徑錯誤 — cloudComputing

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:129` 載入 `data/cloudComputing.md`，實際檔案為 `data/map-CloudComputing.md` |
| **影響** | 雲計算技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-CloudComputing.md` |

---

### 落差 6：`script.js` 載入路徑錯誤 — devLang（無對應單一檔案）

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js` 載入 `data/dev-lang.md`，此檔案不存在；目前最接近的替代為 `data/map-DevLang-Total.md`（另有各語言子文件） |
| **影響** | 開發語言技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-DevLang-Total.md`，或考慮分拆為各語言分頁 |

---

### 落差 7：`script.js` 載入路徑錯誤 — IH（智能硬件）

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:131` 載入 `data/IH.md`，此檔案不存在；最接近的替代為 `data/map-EmbeddedEngineer.md` |
| **影響** | 智能硬件 / 嵌入式技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-EmbeddedEngineer.md`；若兩者語意不同，需另行確認 |

---

### 落差 8：`script.js` 載入路徑錯誤 — IOAM（智能運維）

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:132` 載入 `data/IOAM.md`，此檔案不存在；最接近的替代為 `data/map-IntelligentDevOps.md` |
| **影響** | 智能運維技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-IntelligentDevOps.md` |

---

### 落差 9：`script.js` 載入路徑錯誤 — security

| 項目 | 內容 |
|------|------|
| **問題描述** | `js/script.js:133` 載入 `data/security.md`，此檔案不存在；最接近的替代為 `data/map-SecurityEngineer.md` |
| **影響** | 安全工程師技能樹無法顯示 |
| **建議修復** | 將路徑改為 `data/map-SecurityEngineer.md` |

---

### 落差 10：`index.html` 下載連結失效

| 項目 | 內容 |
|------|------|
| **問題描述** | `index.html:82` 提供 `<a href="data/frontEnd.md" download="...">` 下載連結，但 `data/frontEnd.md` 不存在 |
| **影響** | 使用者點擊下載按鈕會收到 404 錯誤，無法下載原始 Markdown 檔案 |
| **建議修復** | 更新 href 為 `data/map-FrontEndEngineer.md`；同時需檢查其他所有下載連結是否有相同問題 |

---

## 3. 最嚴重問題：mdList 路徑錯誤（7 個路徑全錯）

### 問題說明

`js/script.js:126-134` 定義了 `mdList` 物件，用於指定各技能圖譜 Markdown 文件的載入路徑。在 `data/` 目錄結構重構後（所有檔案加上 `map-` 前綴並調整命名），此物件**從未同步更新**，導致網站啟動後所有 XHR 請求均返回 404，技能樹完全無法顯示。

**影響評估**：網站核心功能完全失效。執行 `npm start` 後打開瀏覽器，所有 7 個技能圖譜區域均為空白。

### 完整修復方案（新舊路徑對照）

需修改 `js/script.js:126-134`，將 `mdList` 更新如下：

| key | 舊路徑（錯誤） | 新路徑（正確） |
|-----|--------------|--------------|
| `devLang` | `data/dev-lang.md` | `data/map-DevLang-Total.md` |
| `bigData` | `data/big-data.md` | `data/map-BigDataEngineer.md` |
| `cloudComputing` | `data/cloudComputing.md` | `data/map-CloudComputing.md` |
| `frontEnd` | `data/frontEnd.md` | `data/map-FrontEndEngineer.md` |
| `IH` | `data/IH.md` | `data/map-EmbeddedEngineer.md` |
| `IOAM` | `data/IOAM.md` | `data/map-IntelligentDevOps.md` |
| `security` | `data/security.md` | `data/map-SecurityEngineer.md` |

**修復後的程式碼**（`js/script.js:126-134`）：

```javascript
var mdList = {
  'devLang':        'data/map-DevLang-Total.md',
  'bigData':        'data/map-BigDataEngineer.md',
  'cloudComputing': 'data/map-CloudComputing.md',
  'frontEnd':       'data/map-FrontEndEngineer.md',
  'IH':             'data/map-EmbeddedEngineer.md',
  'IOAM':           'data/map-IntelligentDevOps.md',
  'security':       'data/map-SecurityEngineer.md'
};
```

**同步需要修復的位置**：
- `index.html:82` — `frontEnd.md` 下載連結（至少需確認所有 `<a download>` 的 href 是否指向實際存在的檔案）

---

## 4. 程式碼中的 TODO / FIXME / HACK

執行指令：

```bash
grep -rn "TODO\|FIXME\|HACK\|XXX\|hack\|todo\|fixme" \
  /home/user/skill-map/js/ \
  /home/user/skill-map/index.html \
  /home/user/skill-map/sass/ \
  --include="*.js" --include="*.html" --include="*.sass"
```

**結果：無發現。**

程式碼中沒有任何 TODO、FIXME、HACK 或 XXX 標記。所有技術債均以隱式方式存在（過時的 API 使用、舊版依賴），開發者未留下明確的問題標記。

---

## 5. 技術債清單

### TD-01：同步 XHR（已廢棄 API）

| 項目 | 內容 |
|------|------|
| **位置** | `js/script.js:91`：`rawFile.open("GET", file, false)` |
| **問題** | 第三個參數 `false` 表示同步請求，會**阻塞主線程**直到回應返回。此 API 在現代瀏覽器中已被標記為 deprecated，部分瀏覽器開始在 DevTools 中顯示警告 |
| **影響** | 頁面載入期間 UI 凍結；未來可能被瀏覽器完全禁用 |
| **建議** | 改用非同步 `fetch()` API，搭配 `Promise.all()` 並行載入所有 Markdown 文件（見 Priority 4） |

### TD-02：Prototype Monkey Patching

| 項目 | 內容 |
|------|------|
| **位置** | `js/script.js:9-60`：直接擴展 `HTMLElement.prototype` |
| **問題** | 為全域 prototype 新增自定義方法（`addClass`、`removeClass`、`hasClass`、`openNode` 等）是反模式，可能與瀏覽器未來原生 API 或第三方 library 產生衝突 |
| **影響** | 目前無實際問題（專案無其他 library），但在引入任何現代框架時可能造成難以追蹤的 bug |
| **建議** | 改用 `classList` API（`element.classList.add/remove/contains()`）或封裝獨立的 utility 函式，避免污染全域 prototype |

### TD-03：Gulp 3.x + gulp-sass 2.x — 過時版本

| 項目 | 內容 |
|------|------|
| **位置** | `package.json:20-25` |
| **問題** | Gulp 目前最新為 4.x（API 重大變更），`gulp-sass@2.x` 底層依賴 `node-sass`（原生 binding），在 Node.js 12+ 環境可能**無法安裝**（binding 需要重新編譯） |
| **影響** | `npm install` 在現代 Node.js 環境可能失敗；`gulp sass` 任務可能無法執行，SASS 編譯鏈中斷 |
| **建議** | 升級至 Gulp 4.x；將 `gulp-sass` 升級至 5.x 並切換底層引擎為 `sass`（Dart Sass，純 JS，無原生 binding 問題） |

### TD-04：marked.js 直接 bundle 至 repo

| 項目 | 內容 |
|------|------|
| **位置** | `js/marked.min.js` |
| **問題** | 第三方 library 直接 commit 至 repo（壓縮版，版本號不透明），難以追蹤版本、進行安全更新 |
| **影響** | 若 marked.js 存在已知 XSS 漏洞，無法透過 `npm audit` 偵測；更新需手動替換檔案 |
| **建議** | 將 marked.js 改為 `package.json` 的 `dependencies`（或 `devDependencies`），透過 npm 管理版本，由建置流程複製至 `js/` |

### TD-05：ES5 語法（無 module system）

| 項目 | 內容 |
|------|------|
| **位置** | `js/script.js` 全檔 |
| **問題** | 使用 `var`、傳統 `function`、IIFE 模式；無 `import/export`、無 `class`、無箭頭函式 |
| **影響** | 可讀性較差；無法使用現代工具鏈（Tree-shaking、bundler 優化）；不易吸引現代前端開發者貢獻 |
| **建議** | 在有維護資源的前提下，逐步重構為 ES Modules（`type="module"`）；短期可保留現狀 |

### TD-06：無自動化測試

| 項目 | 內容 |
|------|------|
| **位置** | 根目錄 `test.md`（只是 Markdown 草稿，非測試框架） |
| **問題** | 專案完全沒有自動化測試（Unit Test、Integration Test、E2E Test） |
| **影響** | 任何程式碼修改（如修復 mdList 路徑）均無安全網；回歸問題難以偵測 |
| **建議** | 至少新增基本的 E2E 測試（如 Playwright 或 Cypress），驗證頁面載入後技能樹節點能正常展開 |

### TD-07：無 CI/CD Pipeline

| 項目 | 內容 |
|------|------|
| **位置** | 專案根目錄（缺少 `.github/workflows/`） |
| **問題** | 沒有 GitHub Actions 或其他 CI/CD 設定，部署完全依賴手動執行 `gulp deploy` |
| **影響** | 貢獻者的 PR 無法自動驗證；部署流程非標準化，依賴本地環境 |
| **建議** | 新增 GitHub Actions workflow：PR 時自動驗證 SASS 能編譯成功；merge 到 main 時自動部署至 GitHub Pages |

---

## 6. 未解答的疑問

### Q1：官方網站 http://skill-map.stuq.org/ 是否仍在運行？

**狀態**：未驗證  
Web 搜尋顯示此網站對應 GitHub Pages 部署（透過 `gulp deploy` 推送至 `gh-pages` 分支）。由於這是外部服務，需要實際發送 HTTP 請求才能確認。目前的程式碼偵察無法判斷。

### Q2：GitHub Pages 是否已正確設定？

**狀態**：未驗證  
`gulpfile.js` 中存在 `gulp deploy` 任務，會將所有檔案推送至 `gh-pages` 分支。但 GitHub 倉庫的 Pages 設定（Settings → Pages）是否已啟用、Domain 是否已設定，需要透過 GitHub API 或直接查看倉庫設定才能確認。

### Q3：為何 `data/` 目錄的檔案改名後，`index.html` 和 `script.js` 未同步更新？

**狀態**：推測性分析  
根據現有證據，最可能的情境是：貢獻者重構了 `data/` 目錄（將舊的 `frontEnd.md`、`big-data.md` 等改名為 `map-FrontEndEngineer.md`、`map-BigDataEngineer.md` 等），但這次重構的 PR/commit 未包含對 `script.js` 和 `index.html` 的對應更新，且 code review 未能捕捉到這個漏洞。由於沒有 CI/CD 或 E2E 測試，這個問題在合併後也未被自動偵測。

### Q4：專案是否仍在積極維護？

**狀態**：需確認 git log  
GitHub 頁面顯示 19.4k stars 和 4.4k forks，代表社群關注度仍高。但 stars/forks 數量不代表活躍維護。需透過 `git log --oneline -20` 查看最後 commit 時間，以及 GitHub Issues/PR 的最後活動時間，才能判斷維護狀態。

### Q5：XMind 文件是否與 Markdown 文件同步維護？

**狀態**：未驗證  
`xmind/` 目錄中有 11 個 `.xmind` 文件，而 `data/` 目錄有 40+ 個 `.md` 文件。兩者數量不符（11 vs 40+），可能意味著 XMind 版本只涵蓋部分技能圖譜，或已停止同步更新。需要逐一比對 xmind/ 檔名與 data/ 檔名才能確認覆蓋範圍。

---

## 7. 建議優先修復事項（按影響排序）

### Priority 1：修復 mdList 路徑（7 個路徑全錯）→ 恢復基本功能

**影響**：Critical — 網站核心功能完全失效  
**工作量**：極低（修改 `js/script.js` 約 7 行 + `index.html` 下載連結）  
**步驟**：
1. 更新 `js/script.js:126-134` 的 `mdList` 物件（見第 3 節對照表）
2. 搜尋 `index.html` 中所有 `href="data/*.md"` 的 `<a download>` 連結，逐一確認目標檔案存在，更新失效路徑

---

### Priority 2：更新 `index.html` 納入新增的 30+ 技能圖譜

**影響**：High — 大量內容對使用者不可見  
**工作量**：中等（需為每個新圖譜新增 HTML 區塊並同步更新 `mdList`）  
**步驟**：
1. 列出 `data/map-*.md` 的完整清單（共 40+ 個）
2. 對照 `index.html` 現有的 7 個 `<li>` 區塊，找出缺少的條目
3. 依照 extensions.md 的 HTML 模板，為每個缺少的圖譜在 `index.html` 的 `<ul id="rootList">` 中新增 `<li>` 區塊
4. 在 `js/script.js` 的 `mdList` 中新增對應的鍵值對

---

### Priority 3：更新 Gulp 至 v4，解決 Node.js 相容性問題

**影響**：Medium — 開發環境可能無法建立  
**工作量**：中等（Gulp 3→4 API 不相容，`gulpfile.js` 需重寫）  
**步驟**：
1. 將 `package.json` 中 `gulp` 升級至 `^4.0.0`
2. 將 `gulp-sass` 升級至 `^5.0.0`，新增 `sass`（Dart Sass）為依賴
3. 將 `gulpfile.js` 的任務語法從 `gulp.task('name', fn)` 重構為 `exports.sass = function() { ... }`（Gulp 4 語法）
4. 確認 `gulp deploy` 任務所用的 `gulp-gh-pages` 是否仍相容，必要時改用 `gh-pages` npm 包

---

### Priority 4：將同步 XHR 改為非同步（fetch API）

**影響**：Medium — 改善效能與未來相容性  
**工作量**：中等（需重構 `readTextFile` 函式及其呼叫鏈，引入非同步流程）  
**步驟**：
1. 將 `readTextFile(file)` 改為回傳 `Promise`（使用 `fetch(file).then(r => r.text())`）
2. 使用 `Promise.all()` 並行請求所有 Markdown 文件，取代目前的同步迴圈
3. 將 `window.onload` 回調改為 `async function`，`await Promise.all(...)` 後再執行樹狀 UI 建立邏輯
4. 移除 IE ActiveXObject fallback（如不再需要支援 IE）

---

*本文件由自動化偵察流程產出，最後更新：2026-04-27*
