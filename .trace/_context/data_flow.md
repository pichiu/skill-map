# 2.2 Data Flow（資料流）

## 代表性 Use Case：使用者瀏覽「Web 前端」技能圖譜

### 完整流程追蹤

```
使用者輸入 http://localhost:9999/
        │
        ▼
[1] 瀏覽器請求 index.html
    http-server 回應靜態檔案
        │
        ▼
[2] 瀏覽器解析 HTML，載入依賴
    ├─ css/style.css（樣式）
    ├─ js/marked.min.js（Markdown 解析器）
    └─ js/script.js（應用邏輯）
        │
        ▼
[3] script.js 第 214 行立即執行：
    skillmap().init()
        │
        ▼
[4] window.onload 觸發（所有資源載入完成）
    script.js:125
        │
        ▼
[5] 同步 XHR 請求技能圖譜 Markdown 文件
    readTextFile('data/frontEnd.md')  ← script.js:89-97
    XMLHttpRequest.open("GET", "data/frontEnd.md", false)
    XMLHttpRequest.send(null)
    return rawFile.responseText       ← 阻塞主線程直到回應
        │
        ▼
[6] marked() 解析 Markdown → HTML
    $id('frontEnd').innerHTML = marked(rawText)
                                ↑script.js:137
    輸入（Markdown 片段）:
    "# 前端工程師技能圖譜\n## 瀏覽器\n  - IE6/7..."
    輸出（HTML）:
    "<h1>前端工程師技能圖譜</h1><h2>瀏覽器</h2><ul><li>IE6/7...</li>"
        │
        ▼
[7] HTML 注入 DOM
    目標容器：<div class="node" id="frontEnd"></div>
    位於 index.html:83
        │
        ▼
[8] 遍歷所有 <li> 元素，建立可折疊樹結構
    script.js:140-162
    ├─ 有子 li → 加 'closed' class，hide 子節點，綁 click
    └─ 無子 li → 加 'normal' class
        │
        ▼
[9] 使用者點擊 "Web 前端" 標題（h2 元素）
    onclick handler：script.js:147-157
    ├─ 若子節點有 'hide' → 移除 hide，切換 closed→opened
    └─ 若子節點無 'hide' → 加 hide，切換 opened→closed
        │
        ▼
[10] 樹狀技能圖譜展開顯示
     各層級以 CSS list-style-image 顯示 plus/minus 圖示
     (style.sass:112-117)
```

### 資料轉換對照表

| 階段 | 輸入格式 | 輸出格式 | 處理函式/機制 |
|------|---------|---------|--------------|
| 1 資料源 | `.md` Markdown 文字 | 原始字串 | `readTextFile()` / XHR |
| 2 解析 | Markdown 字串 | HTML 字串 | `marked()` |
| 3 渲染 | HTML 字串 | DOM 樹 | `innerHTML` 注入 |
| 4 互動 | DOM 樹 | 可折疊 UI | class 操作（`addClass/removeClass`） |

### Markdown 分級結構如何對應 HTML 樹

README.md 定義的分級規範（README.md:85-91）：

```
# 根節點          → <h1>
## 主幹分類        → <h2>
### 子分類         → <h3>
- 優先級最高      → <ul><li> (marked 預設)
* 次優先級         → <ul><li>
+ 最低優先級       → <ul><li>
```

marked.js 將以上轉換為巢狀 `<ul><li>` 結構，`script.js` 再以 `isLeafNode()`（`script.js:44`）判斷是否有子 `<li>` 來決定是否為可折疊節點。

### 全局展開/折疊

- **展開全部**：`$id('expand').onclick`（`script.js:186`），遍歷所有 `closed` li，切換為 `opened`
- **折疊全部**：`$id('collapse').onclick`（`script.js:196`），遍歷所有 `opened` li，切換為 `closed`
- **回到頂部**：`$id('toTop').onclick`（`script.js:174`），使用 `scrollTo()` 動畫滾動（`script.js:101-114`）

### 錨點導航

`index.html:26-36` 提供快速導航連結（`<a href="#frontEndIndex">` 等），利用瀏覽器原生錨點滾動。

## ⚠️ 當前資料流斷點

由於 `script.js` 中 `mdList` 的檔案路徑與 `data/` 目錄中實際檔案名稱不符：

```javascript
// 期望：      實際存在：
'data/frontEnd.md'      → data/map-FrontEndEngineer.md
'data/big-data.md'      → data/map-BigDataEngineer.md
'data/cloudComputing.md'→ data/map-CloudComputing.md
'data/dev-lang.md'      → data/map-DevLang-Total.md (+ 各語言子文件)
'data/IH.md'            → data/map-EmbeddedEngineer.md
'data/IOAM.md'          → data/map-IntelligentDevOps.md
'data/security.md'      → data/map-SecurityEngineer.md
```

步驟 [5] 的 XHR 請求會收到 404，`readTextFile()` 的 `try/catch` 只 `console.log(error)`，`$id(i).innerHTML` 被設為 `marked(undefined)`，技能樹顯示為空。
