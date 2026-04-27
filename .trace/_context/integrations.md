# 2.5 外部整合

## 整合概覽

這是一個純靜態網站，外部整合非常少，全部為單向輸出（無外部 API 呼叫的回應處理需求）。

## 整合 1：百度統計（Baidu Analytics）

**類型**: 前端網頁分析
**位置**: `index.html:9-17`

```html
<script>
  var _hmt = _hmt || [];
  (function() {
    var hm = document.createElement("script");
    hm.src = "//hm.baidu.com/hm.js?c5521d8cc55de1f615df373b27ee7557";
    var s = document.getElementsByTagName("script")[0];
    s.parentNode.insertBefore(s, s);
  })();
</script>
```

- **用途**: 追蹤頁面瀏覽量、使用者來源
- **載入方式**: 非同步動態注入 `<script>` 標籤，不阻塞頁面渲染
- **Site ID**: `c5521d8cc55de1f615df373b27ee7557`
- **失敗處理**: 無。若 CDN 不可達，腳本靜默失敗，不影響主功能
- **隱私考量**: 無 cookie consent，資料直接送往百度伺服器

## 整合 2：marked.js（Markdown 解析器）

**類型**: 第三方 JavaScript Library（bundled in repo）
**位置**: `js/marked.min.js`（壓縮版，直接 commit 至 repo）

- **用途**: 將 Markdown 字串解析為 HTML 字串
- **調用方式**: `marked(markdownString)`（同步函式呼叫）
- **載入方式**: `<script src="js/marked.min.js">` 同步載入（`index.html:18`）
- **版本**: ⚠️ 未驗證（檔案為壓縮版，版本號未知）
- **失敗處理**: 無。若 marked 解析失敗，`innerHTML` 可能收到空字串或拋出例外

## 整合 3：http-server（開發伺服器）

**類型**: Node.js CLI 工具（devDependency）
**版本**: `^0.8.5`（`package.json:23`）

- **用途**: 本地開發時提供靜態檔案服務
- **啟動**: `npm start` → `http-server . -p 9999 -o`
- **-o flag**: 自動開啟預設瀏覽器
- **失敗處理**: 若 port 9999 被占用，http-server 會報錯退出

## 整合 4：GitHub Pages（部署平台）

**類型**: 靜態網站托管
**工具**: `gulp-gh-pages@^0.5.4`（`package.json:21`）

- **部署指令**: `gulp deploy`（`gulpfile.js:6-9`）
- **機制**: 將 `./**/*` 的所有檔案推送至 `gh-pages` 分支
- **對應域名**: `http://skill-map.stuq.org/` 或 GitHub Pages URL（⚠️ 未驗證當前狀態）
- **失敗處理**: 需要 git 認證；若認證失敗，部署流程拋出錯誤

## 整合 5：外部連結（頁面內容中）

`index.html` 包含多個外部連結，但均為靜態 `<a>` 標籤，無 HTTP 請求發出：

- CC-BY-NC-SA 4.0 授權連結（`index.html:141`）
- 各貢獻者的 GitHub 個人頁面（`README.md` 中）
- 極客時間 App 推廣 QR Code 圖片（`img/GeekTime-QRCode-*.png`，本地資源）

## 整合 6：Gulp Build Chain

**類型**: 建置工具整合

| Plugin | 版本 | 用途 |
|--------|------|------|
| `gulp` | ^3.9.0 | 任務執行器核心 |
| `gulp-sass` | ^2.1.1 | SASS → CSS 編譯 |
| `gulp-sourcemaps` | ^1.6.0 | Source map 生成（方便 debug） |
| `gulp-gh-pages` | ^0.5.4 | GitHub Pages 部署 |

**⚠️ 注意**: Gulp 3.x 是過時版本（目前最新為 Gulp 4.x），且 `gulp-sass@2.x` 依賴的底層 node-sass 對較新的 Node.js 版本不相容，可能造成安裝失敗。

## 沒有的整合

- ❌ 無後端 API
- ❌ 無資料庫
- ❌ 無身份驗證服務
- ❌ 無 CDN（資源直接 serve 自 GitHub Pages）
- ❌ 無 CI/CD pipeline（無 `.github/workflows/`）
- ❌ 無第三方 UI 框架（React/Vue/Angular）
- ❌ 無包管理器驅動的 CSS framework（Bootstrap 等）
