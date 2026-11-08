# EPUB 編輯工坊

在瀏覽器裡處理 EPUB 電子書的單頁工具：簡體轉繁體、標點轉換、直排橫排、字型與行距調整、封面更換，也能把 MOBI／AZW／AZW3 轉成 EPUB。

**所有處理都在你的瀏覽器裡完成，檔案不會上傳到任何伺服器。**

線上使用：<https://epub-editor.parrotchang.workers.dev/epub-editor>

## 功能

### 簡體轉繁體
- 四種轉換方式：不轉換、只轉字形、台灣用字、台灣用字加慣用詞（例如「軟體」「記憶體」）
- 書名、作者、目錄和內文一起轉換，語言標記改為 `zh-TW`
- 使用 [OpenCC](https://github.com/BYVoid/OpenCC) 的 JavaScript 版本

### 標點符號
- 中文內的 `“ ”` `‘ ’` 改為 `「 」` `『 』`
- 中文後面的半形 `, . ! ? : ;` 改為全形

### 排版與字型
- 書寫方向：保持原樣、直排、橫排
- 字型風格：明體、黑體、楷體
- 字級、行距、首行縮排
- 設定區附有即時預覽

### 封面
- 新增、替換或移除封面（支援 JPG、PNG、GIF）

### MOBI／AZW／AZW3 轉 EPUB
- 支援 `.mobi`、`.azw`、`.azw3`、`.prc`，包含舊式 MOBI 與 KF8（AZW3）
- 保留章節、目錄、內部連結、圖片、封面與書籍資訊
- 可以只轉換格式，也可以轉完接著套用上面的編輯功能
- **不支援**有 DRM 的書、使用 HUFF/CDIC 壓縮的舊檔、Topaz 格式（AZW1）
- 內嵌字型不會保留

## 使用方式

1. 開啟頁面，把檔案拖進左邊的書本區，或點一下選擇檔案
2. 在右邊調整設定
3. 按「輸出 EPUB」，完成後按「儲存 EPUB」

建議桌機處理 50MB 以下的檔案，手機 20MB 以下。檔案越大，處理越久。

## 自己部署

整個專案只有一個 HTML 檔，沒有建置步驟，不需要伺服器程式。

```
epub-editor/
└── epub-editor.html
```

可以放在任何靜態網站空間：

- **本機**：直接用瀏覽器開啟 `epub-editor.html`
- **Cloudflare Pages / GitHub Pages / Netlify**：上傳檔案，想用 `/epub-editor/` 這種網址的話，把檔案改名為 `index.html` 放進 `epub-editor` 資料夾

頁面需要連網，因為會從 CDN 載入以下資源：

| 資源 | 用途 | 授權 |
| --- | --- | --- |
| [JSZip](https://stuk.github.io/jszip/) 3.10.1 | 讀寫 EPUB 壓縮檔 | MIT 或 GPLv3 |
| [opencc-js](https://github.com/nk2028/opencc-js) 1.0.5 | 簡繁轉換 | MIT |
| Google Fonts（Noto Sans TC、Noto Serif TC、IBM Plex Mono） | 介面字型 | OFL |

想完全離線使用，可以把這兩個 JavaScript 檔下載後改成相對路徑引用。

## 運作方式

- **EPUB 編輯**：用 JSZip 解開檔案，讀取 `container.xml` 找到 OPF，只轉換 XHTML 標籤之間的文字，不動標籤、屬性、`<style>` 和 `<script>`。字型、字級、行距、縮排和直排橫排，是產生一份 `epub-edit.css` 並加入每個章節，用 `!important` 覆蓋原書樣式。
- **MOBI 轉換**：自行解析 PalmDB 與 MOBI 標頭、EXTH 書籍資訊、PalmDOC 解壓縮與尾端資料，舊式 MOBI 轉為 XHTML 並依分頁標記切章；KF8 則讀取 SKEL／FRAG 索引重組章節，並把 `kindle:pos`、`kindle:embed`、`kindle:flow` 連結改寫成一般路徑。
- 輸出時 `mimetype` 不壓縮並放在壓縮檔第一個位置，且不產生空的資料夾項目，符合 EPUB 規範。

## 已知限制

- 直排與字型是靠加入樣式檔生效，部分閱讀器可能不完全遵守。
- 字級與行距只調整書本最外層設定，書裡自己指定字級的段落不受影響。
- 標點轉換採用簡單規則，遇到特殊排版的書可能需要自行檢查。
- 轉換 MOBI／AZW 的程式用自己做的測試檔驗證過，真實書籍的格式更多樣，遇到轉換錯誤歡迎回報。

## 回報問題

到 [Issues](https://github.com/parrotchang/epub-editor/issues) 回報時，請附上：

- 使用的瀏覽器與版本
- 原始檔案格式（EPUB、MOBI、AZW3）和出問題的畫面或錯誤訊息
- 用哪個閱讀器開啟輸出檔，以及看到什麼狀況

如果書不涉及版權，附上檔案能最快找到原因。請不要上傳有版權的書籍或有 DRM 的檔案。

## 致謝

功能構想參考 HelloRuru 的 [EPUB 編輯器](https://tools.helloruru.com/epub-editor/)。本專案是依公開的功能說明獨立實作，介面與程式碼皆為自行撰寫，與原站沒有關係。

## 授權

[MIT License](./LICENSE)
