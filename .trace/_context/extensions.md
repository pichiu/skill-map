# 2.4 Extension Points（擴展點）

## 擴展方式概覽

這個專案是靜態內容網站，「擴展」主要是內容擴展（新增技能圖譜），而非程式架構擴展。擴展點分為兩類：

1. **內容擴展**（貢獻新的技能圖譜）
2. **功能擴展**（修改網站呈現行為）

## 擴展點 1：新增技能圖譜（主要擴展方式）

### 目前流程

1. 建立 `data/map-{DomainName}.md`，遵循 README.md 的 Markdown 分級規範
2. 準備 PNG 圖片版（放入 `data/designbyStuQ/png-{DomainName}-by-StuQ.png`）
3. 可選：製作 XMind 版（放入 `xmind/{name}.xmind`）
4. **⚠️ 需手動更新 `index.html` 和 `js/script.js`** —— 這是目前沒有自動化的手動步驟

### 需要修改的地方（新增一個 Skill Map）

| 步驟 | 檔案 | 修改內容 |
|------|------|---------|
| 1 | `data/map-NewDomain.md` | 建立 Markdown 內容 |
| 2 | `js/script.js:126-134` | 在 `mdList` 中新增 `'newDomain': 'data/map-NewDomain.md'` |
| 3 | `index.html` | 在 `<ul id="rootList">` 中新增對應 `<li>` 區塊（含 `id="newDomainIndex"` 和 `<div class="node" id="newDomain">`）|
| 4 | `sass/style.sass`（可選） | 若需要特定樣式，新增 CSS 規則 |

### 新增 `<li>` 的 HTML 範本

```html
<li id="{domain}Index">
  <h2>{領域名稱}</h2>
  <span>（貢獻者：@contributor；下載：
    <a href="data/map-{Domain}.md" download="{領域}(StuQ 技能圖譜).md">{領域}.md</a> | 
    <a href="data/designbyStuQ/png-{Domain}-by-StuQ.png" download="{領域}.png">{領域}.png</a>
  ）</span>
  <div class="node" id="{domain}"></div>
</li>
```

`id` 要對應 `mdList` 的 key（如 `newDomain`），`<div>` 的 `id` 也要相同。

## 擴展點 2：調整預設展開節點

**位置**: `js/script.js:116-122`

```javascript
var defaultOpen = [
  'index',
  'intro',
  'language',
  'end'
];
```

修改此陣列即可控制頁面載入時預設展開哪些頂層節點。值必須對應 HTML 中 `<li>` 元素的 `id` 屬性。

## 擴展點 3：樣式擴展

**SASS 結構**:
- `sass/_base.sass` — 基礎變數和 Reset
- `sass/style.sass` — 主要樣式（`@import base` 引入 `_base`）

新增自訂樣式：
1. 在 `sass/style.sass` 中新增規則
2. 執行 `npm run gulp`（或 `gulp sass`）重新編譯 CSS

## 擴展點 4：IE 兼容性降級

**位置**: `js/script.js:66-86`

XHR 物件建立時包含完整的 IE ActiveXObject fallback 鏈：
```
XMLHttpRequest → MSXML2.XmlHttp.5.0 → 4.0 → 3.0 → 2.0 → Microsoft.XmlHttp
```

DOM Prototype 也有 IE 降級：`Element.prototype`（IE8 以下）代替 `HTMLElement.prototype`（現代瀏覽器）

## 限制：沒有 Plugin 系統

目前架構沒有正式的 Plugin 系統、Hook 機制、Middleware chain 或 Event emitter。
所有「擴展」都是直接修改源碼。

若要添加事件驅動的插件系統，需要重構 `skillmap` 模組，新增類似以下的機制：
```javascript
skillmap.on('nodeOpen', callback)
skillmap.on('contentLoaded', callback)
```
但目前版本並無此需求。

## 注意：Markdown 格式即是協議

整個系統的「介面」定義在 `README.md:83-91` 的 Markdown 分級規範。任何貢獻者按此規範撰寫 `.md` 文件，`marked.js` 就能正確解析，樹狀結構就能自動生成。這是系統最重要的「隱式擴展介面」。
