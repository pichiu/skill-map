# 2.6 設定與環境

## 設定總覽

這個靜態網站幾乎沒有「設定」的概念。所有行為都硬編碼在源碼中，沒有 env vars、config files 或 feature flags。

## 唯一的「設定」：package.json scripts

**位置**: `package.json:6-9`

```json
"scripts": {
  "start": "http-server . -p 9999 -o",
  "gulp": "gulp"
}
```

| 參數 | 值 | 含義 |
|------|----|------|
| Port | 9999 | 本地開發伺服器端口（hardcoded） |
| `-o` | 自動開啟瀏覽器 | 啟動後自動打開頁面 |

若需要更改 port，直接修改 `package.json:7`。

## 應用內設定（硬編碼在 script.js）

| 設定項 | 位置 | 當前值 | 說明 |
|--------|------|--------|------|
| 預設展開節點 | `script.js:116-122` | `['index', 'intro', 'language', 'end']` | 頁面載入時展開的頂層 `<li>` id 列表 |
| 技能圖譜 Markdown 路徑 | `script.js:126-134` | 見下表 | 各技能圖譜的載入路徑 |

### 技能圖譜路徑映射（`script.js:126-134`）

```javascript
var mdList = {
  'devLang':        'data/dev-lang.md',        // ⚠️ 路徑錯誤
  'bigData':        'data/big-data.md',        // ⚠️ 路徑錯誤
  'cloudComputing': 'data/cloudComputing.md',  // ⚠️ 路徑錯誤
  'frontEnd':       'data/frontEnd.md',        // ⚠️ 路徑錯誤
  'IH':             'data/IH.md',              // ⚠️ 路徑錯誤
  'IOAM':           'data/IOAM.md',            // ⚠️ 路徑錯誤
  'security':       'data/security.md'         // ⚠️ 路徑錯誤
};
```

所有路徑均指向不存在的舊檔名，應更正為：

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

## Gulp 建置設定

**位置**: `gulpfile.js`

```javascript
// SASS 編譯任務
gulp.task('sass', function () {
  return gulp.src('sass/style.sass')        // 輸入
    .pipe(sourcemaps.init())               // 開始記錄 source map
    .pipe(sass())                          // 編譯 SASS
    .pipe(sourcemaps.write('.'))           // 輸出 .css.map
    .pipe(gulp.dest('css/'));              // 輸出目錄
});

// GitHub Pages 部署任務
gulp.task('deploy', function() {
  return gulp.src('./**/*')               // 所有檔案
    .pipe(ghPages());                     // 推送至 gh-pages 分支
});
```

沒有任何環境變數控制，也沒有 production/development 模式區別。

## .gitignore 設定

**位置**: `.gitignore`

```
.DS_Store        # macOS 系統目錄索引
.sass-cache      # SASS 編譯快取
node_modules     # npm 依賴（不 commit）
*log             # 任何 log 檔案
*logs            # 任何 logs 目錄
.publish         # gulp-gh-pages 的暫存目錄
```

## 無以下設定機制

- ❌ 無 `.env` / `.env.example`（沒有環境變數需求）
- ❌ 無 `config/` 目錄
- ❌ 無 Feature Flags
- ❌ 無 Secrets 管理（只有百度統計的 Site ID 硬編碼在 HTML 中）
- ❌ 無多環境設定（dev/staging/production）
- ❌ 無 CI/CD 環境設定（無 GitHub Actions / Travis CI 等）
