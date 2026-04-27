# 2.1 Entry Points

## 程式入口

這是一個**純靜態前端網站**，沒有伺服器端啟動程序。唯一的「入口」是瀏覽器載入 HTML 頁面。

### 頁面載入流程

```
瀏覽器請求 index.html
  └─ <link href="css/style.css">      # 靜態樣式載入
  └─ <script> Baidu Analytics        # index.html:10-17，行內腳本，非同步注入 hm.js
  └─ <script src="js/marked.min.js"> # index.html:18，Markdown 解析器
  └─ <script src="js/script.js">     # index.html:142，應用邏輯
       └─ skillmap().init()          # script.js:214，立即執行
            └─ window.onload = ...   # script.js:125，等待 DOM 完全載入
                 ├─ 同步 XHR 載入各 .md 文件
                 ├─ marked() 解析 Markdown → HTML
                 ├─ 注入 DOM
                 └─ 綁定互動事件
```

### JavaScript 入口函式

**檔案**: `js/script.js:214`
```javascript
skillmap().init();
```

- `skillmap` 是一個工廠函式（Factory Function），定義於 `script.js:64`
- 回傳物件 `{ init: init }`（Revealing Module Pattern）
- `init()` 設定 `window.onload` callback，確保 DOM 就緒後才執行

### window.onload 回調（`script.js:125-205`）

執行以下 3 步驟初始化：

**步驟 1：載入 Markdown 內容**（`script.js:126-138`）

```javascript
var mdList = {
  'devLang':       'data/dev-lang.md',       // ⚠️ 檔案不存在
  'bigData':       'data/big-data.md',       // ⚠️ 檔案不存在（應為 map-BigDataEngineer.md）
  'cloudComputing':'data/cloudComputing.md', // ⚠️ 檔案不存在
  'frontEnd':      'data/frontEnd.md',       // ⚠️ 檔案不存在
  'IH':            'data/IH.md',             // ⚠️ 檔案不存在
  'IOAM':          'data/IOAM.md',           // ⚠️ 檔案不存在
  'security':      'data/security.md'        // ⚠️ 檔案不存在
};
for (var i in mdList) {
  $id(i).innerHTML = marked(readTextFile(mdList[i]));
}
```

`readTextFile()` 使用**同步 XHR**（`rawFile.open("GET", file, false)`，`script.js:91`）

**步驟 2：建立可折疊樹狀結構**（`script.js:140-162`）

遍歷所有 `<li>` 元素：
- 有子 `<li>`（非葉節點）→ 加上 `closed` class，綁定 click 事件
- 無子 `<li>`（葉節點）→ 加上 `normal` class

**步驟 3：預設展開指定節點**（`script.js:164-167`）

```javascript
var defaultOpen = ['index', 'intro', 'language', 'end'];
for (var i = 0; i < defaultOpen.length; i++) {
  $id(defaultOpen[i]).openNode();
}
```

## 開發伺服器入口

**指令**: `npm start`

執行 `http-server . -p 9999 -o`：以 port 9999 提供整個根目錄的靜態檔案，並自動開啟瀏覽器。

## 建置工具入口

**指令**: `npm run gulp`（或直接 `gulp`）

- `gulp sass`：編譯 `sass/style.sass` → `css/style.css`（`gulpfile.js:11`）
- `gulp deploy`：將所有檔案推送至 GitHub Pages（`gulpfile.js:6`）

## 初始化注意事項

- 沒有 DI container、middleware 初始化、DB 連線等伺服器端概念
- 百度統計為唯一的「第三方初始化」，在 `<head>` 中非同步載入，不影響主邏輯
- 同步 XHR 在現代瀏覽器中已被 deprecated（主線程阻塞），但因為是靜態網站本地載入，速度影響有限
