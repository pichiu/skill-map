# 2.3 核心領域邏輯

## 這個專案的「心臟」

這個專案的核心不是複雜的業務邏輯，而是兩個關鍵設計：

1. **Markdown → 可折疊 HTML 樹**的轉換管道
2. **DOM 操作的 Prototype Extension 模式**

## 核心抽象 1：DOM Prototype Extension

**檔案**: `js/script.js:9-60`（IIFE 包裝）

直接擴展 `HTMLElement.prototype`（或 IE 的 `Element.prototype`），為所有 DOM 元素新增方法：

```javascript
var elementPrototype = typeof HTMLElement !== 'undefined' 
  ? HTMLElement.prototype 
  : Element.prototype;  // IE 降級

// 工具方法
elementPrototype.tagEles = function(tagName) { ... }      // 封裝 getElementsByTagName
elementPrototype.hasClass = function(className) { ... }   // RegExp 字串匹配 className
elementPrototype.addClass = function(className) { ... }   // 防重複新增
elementPrototype.removeClass = function(className) { ... } // RegExp 替換移除

// 節點狀態方法
elementPrototype.isLeafNode = function() {                // 判斷是否有子 <li>
  return this.tagEles('li').length ? false : true;
};
elementPrototype.openNode = function() {                  // 展開：移除 hide，設 opened
  this.lastElementChild.removeClass('hide');
  this.removeClass('closed');
  this.addClass('opened');
};
elementPrototype.normalizeNode = function() {             // 固定展開（無折疊功能）
  this.lastElementChild.removeClass('hide');
  this.firstElementChild.removeClass('node');
  this.firstElementChild.style.cursor = 'initial';
  this.addClass('normal');
  this.removeClass('closed');
};
```

**設計模式**: Prototype Extension（猴子補丁 Monkey Patching）
**Trade-off**: 簡化了呼叫語法（`element.addClass()`），但在現代 JavaScript 中被視為反模式（污染全域 prototype），因為可能與未來的瀏覽器原生 API 衝突（`classList` API 是更好的替代方案）

## 核心抽象 2：skillmap 模組

**檔案**: `js/script.js:63-212`（IIFE 包裝）

採用 **Revealing Module Pattern**：

```javascript
var skillmap = window.skillmap = function() {
  // 私有函式
  var newRequest = function() { ... };    // XHR 工廠（含 IE ActiveXObject 降級）
  var readTextFile = function(file) { ... }; // 同步 XHR 讀取文字檔
  var scrollTo = function(...) { ... };   // 遞迴平滑捲動動畫
  var defaultOpen = ['index', 'intro', 'language', 'end'];
  
  var init = function() { ... };          // 主初始化函式
  
  return { init: init };                  // 只暴露 init
};
skillmap().init();                        // 立即實例化並執行
```

**設計模式**: Revealing Module Pattern + IIFE
- 封裝私有狀態，只暴露最小 API
- 兼容 ES5（無 class、無 import/export）

## 核心演算法：樹狀 UI 建立

**位置**: `js/script.js:140-167`

### isLeafNode 判斷邏輯

```javascript
elementPrototype.isLeafNode = function() {
  return this.tagEles('li').length ? false : true;
};
```

這個判斷的關鍵：`getElementsByTagName('li')` 是**後代搜尋**（不是只找直接子節點）。任何包含 `<li>` 的 `<li>` 都被視為非葉節點，會被設為可折疊。

### 樹的展開/折疊狀態機

每個 `<li>` 節點有三種互斥狀態：

| CSS Class | 含義 | list-style-image |
|-----------|------|-----------------|
| `closed` | 已折疊（子節點隱藏） | `plus.png`（`+`） |
| `opened` | 已展開（子節點可見） | `minus.png`（`-`） |
| `normal` | 葉節點（無子節點） | `none` |

狀態轉換由 `addClass/removeClass` 驅動，CSS 透過屬性選擇器控制視覺效果。

### 平滑捲動演算法

**位置**: `js/script.js:101-114`

```javascript
var scrollTo = function(element, to, duration) {
  if (duration < 0) {
    $id('toTop').addClass('hide');
    return;
  }
  var difference = to - element.scrollTop;
  var perTick = difference / duration * 2;
  setTimeout(function() {
    element.scrollTop = element.scrollTop + perTick;
    scrollTo(element, to, duration - 2);
  }, 10);
};
```

**遞迴計時器動畫**（非 `requestAnimationFrame`），每 10ms 更新一次滾動位置，duration 遞減至 < 0 時停止。這是 2015 年的實作方式，現代替代方案為 `element.scrollIntoView({ behavior: 'smooth' })`。

## Markdown 內容的分級規範（非程式邏輯，但是核心規則）

**定義位置**: `README.md:83-91`

```
# 根節點（最高級別主幹）
## 次主幹
### 三級主幹
- 一級分支（優先級最高）
* 二級分支
+ 三級分支（優先級最低）
```

這個規範定義了所有 `data/map-*.md` 文件的撰寫標準，`marked.js` 按此規範產生巢狀 HTML 樹。

## 全局變數/命名空間

| 變數 | 位置 | 用途 |
|------|------|------|
| `window.skillmap` | script.js:64 | skillmap 工廠函式（暴露為全域） |
| `window.$id` | script.js:13 | `document.getElementById` 的別名 |
| `window.$tag` | script.js:16 | `document.getElementsByTagName` 的別名 |
| `window._hmt` | index.html:10 | 百度統計全域佇列 |
