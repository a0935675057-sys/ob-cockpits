# 新增章節 ‧ 標準作業流程 (SOP)

如何在 `ob-cockpits` 倉庫新增一個教學駕駛艙(例如第二章),並部署到 GitHub Pages。

> 線上總覽:<https://a0935675057-sys.github.io/ob-cockpits/>
> 倉庫:<https://github.com/a0935675057-sys/ob-cockpits>

---

## 0. 事前準備(每章一次)

準備好該章的原始素材,通常包含:

| 素材 | 用途 | 在駕駛艙裡對應 |
| --- | --- | --- |
| 教學簡報 PDF | 轉成簡報圖 | 「NotebookLM 簡報」分頁 |
| 資訊圖表 PNG(數張) | 直接使用 | 「資訊圖表」分頁 |
| 學生自學指南 / 講稿(文字) | 出題與設計互動 | 「形成性評量」「互動視覺化」 |
| YouTube 影片網址 | 嵌入播放 | 「教學影片」分頁 |

工具需求:`git`、`gh`(已登入)、`python`(含 `pymupdf` 套件,用來轉 PDF)。

---

## 1. 命名規則(務必遵守)

GitHub Pages 區分大小寫,檔名請固定格式:

```
CH2/                         ← 章節資料夾,大寫 CH + 數字
├── index.html               ← 主程式(檔名一定是 index.html)
├── slides/
│   ├── slide_01.png         ← 兩位數補零:slide_01、slide_02 …
│   └── …
└── Ch2_資訊圖表1_主題.png    ← 資訊圖表
```

- 章節資料夾:`CH2`、`CH3`…(大寫)
- 簡報圖:`slide_01.png` 起,**兩位數補零**
- 主程式:**一定叫 `index.html`**(Pages 會自動開啟)

---

## 2. 轉換簡報 PDF → 簡報圖

在該章素材資料夾內執行(把 `來源.pdf` 換成實際檔名):

```bash
mkdir -p slides
python -c "
import fitz                       # pymupdf
doc = fitz.open('來源.pdf')
mat = fitz.Matrix(1.15, 1.15)     # 約 1583px 寬,清晰度足夠又不過大
for i, page in enumerate(doc, 1):
    pix = page.get_pixmap(matrix=mat)
    pix.save(f'slides/slide_{i:02d}.png')
print('完成,共', doc.page_count, '頁')
"
```

> ⚠️ 本機的 poppler(`pdftoppm`)在此環境會 segfault,請用上面的 **pymupdf(fitz)** 方法。
> 若檔名是中文造成問題,先 `cp 中文.pdf src.pdf` 改成英文檔名再轉。

---

## 3. 製作該章的 `index.html`

最快的方式:**複製第一章的 `CH1/index.html`**,再改裡面的「資料設定區」即可——版面、互動工具、教師/學生模式都會沿用,不必重寫。

開啟新章 `index.html`,只需修改 `<script>` 開頭的幾個設定區:

### (a) `INFO` — 資訊圖表檔名

```js
const INFO = {
  1:'Ch2_資訊圖表1_主題A.png',
  2:'Ch2_資訊圖表2_主題B.png',
  // …依張數增減
};
```

### (b) `VIDEOS` — 各教學重點的 YouTube 網址

```js
const VIDEOS = {
  '01':'https://www.youtube.com/watch?v=XXXX',
  '02':'https://www.youtube.com/watch?v=XXXX',
  // …五個重點同一支就全填一樣;不同支就各自填;留 '' 代表空白
};
```

### (c) `TP` — 五大教學重點主資料(核心)

每個重點一個物件:

```js
{
  no:'01',
  title:'重點標題',
  obj:'學習目標一句話',
  slides:[                                   // 這個重點用到哪幾張簡報(對應 slide_NN)
    {n:1, note:'教師導引文字(學生模式會隱藏)'},
    {n:2, note:'…'},
  ],
  info:1,                                    // 對應上面 INFO 的編號
  infoCap:'資訊圖表 1 ‧ 圖說',
  interactive:'myth',                        // 互動類型,見下表
  quiz:[                                     // 形成性評量(選擇題)
    {q:'題目', opts:['A','B','C','D'], a:1, exp:'解析(a 是正確選項索引,0 起算)'},
    // 建議每個重點 3 題
  ]
},
```

### (d) 互動視覺化類型

`interactive` 欄位可填以下五種(沿用 CH1 既有元件):

| 類型 | 互動形式 | 對應資料陣列(在 script 內) |
| --- | --- | --- |
| `myth` | 迷思 vs 事實判斷器 | `MYTHS` |
| `classify` | 卡片拖放分類(分三類) | `ROLES` + `ROLE_CATS` |
| `levels` | 主題對應層次配對 | `LEVELS_ITEMS` + `LEVEL_CATS` |
| `sim` | 條件模擬器(切換條件看結果) | 函式 `ivSim` 內的邏輯 |
| `engine` | 流程鏈點亮 | `ENGINE` |

> 互動內容是「依該章主題客製」的,需要改對應的資料陣列。
> 最省事的做法:把該章素材交給 Claude,直接請它依本 SOP 產生整份 `CHx/index.html`。

### (e) 頂部標題

搜尋 `組織行為學 ‧ 教學駕駛艙` 與 `CHAPTER 1`,改成該章名稱。

---

## 4. 放進倉庫

把做好的章節資料夾複製進本地倉庫:

```bash
# 假設本地倉庫在 C:\Users\Ken\Desktop\Claudeskills\ob-cockpits
cp -r /路徑/CH2 "/c/Users/Ken/Desktop/Claudeskills/ob-cockpits/CH2"
```

確認結構:`CH2/index.html`、`CH2/slides/`(齊全)、`CH2/Ch2_資訊圖表*.png`。

---

## 5. 在總覽首頁加一張卡片

編輯倉庫根目錄 `index.html`,在 `<div class="grid">` 內、`<!-- 日後新增章節 -->` 註解上方,複製一張卡片並修改:

```html
<a class="card" href="CH2/index.html">
  <div class="no">02</div>
  <div class="ti">章節名稱 ‧ 第二章</div>
  <div class="de">本章重點的一句話簡介。</div>
  <div class="tags"><span class="tag">5 大重點</span><span class="tag">N 張簡報</span><span class="tag">N 資訊圖表</span><span class="tag">互動 + 評量</span></div>
  <div class="go">▶ 進入駕駛艙</div>
</a>
```

---

## 6. 提交並部署

```bash
cd "/c/Users/Ken/Desktop/Claudeskills/ob-cockpits"
git add -A
git commit -m "新增第二章教學駕駛艙"
git push
```

GitHub Pages 會自動重新建置,約 1~2 分鐘後更新。

---

## 7. 驗證上線

```bash
BASE="https://a0935675057-sys.github.io/ob-cockpits"
# 等建置完成
gh api repos/a0935675057-sys/ob-cockpits/pages/builds/latest --jq '.status'   # 應為 built

# 檢查頁面與資產(中文檔名需先百分比編碼)
curl -s -o /dev/null -w "%{http_code}\n" "$BASE/CH2/"
curl -s -o /dev/null -w "%{http_code}\n" "$BASE/CH2/slides/slide_01.png"
```

瀏覽器打開 `$BASE/` 應看到新卡片;點進去簡報、資訊圖表、影片、互動、評量都正常即完成。

---

## 常見問題 / 注意事項

- **圖片 404**:多半是檔名大小寫或路徑不符。確認 `INFO`、`slide_NN` 命名與實際檔案完全一致。
- **影片分頁空白**:`VIDEOS` 沒填網址。學生端看到的是 `VIDEOS` 內容(瀏覽器暫存不會跟著檔案走)。
- **檔案太大**:不要把原始 PDF、影片 mp4 放進倉庫;只放 `index.html` + `slides/` + 資訊圖表 PNG。單一檔案勿超過 100MB。
- **中文檔名**:可正常運作(瀏覽器自動編碼),但若想最保險可改英文檔名(需同步改 HTML 引用)。
- **不要動 `.nojekyll`**:它確保中文檔名與資料夾被原樣提供,刪掉可能導致部分檔案 404。

---

## 最省事的流程(推薦)

> 把該章的「簡報 PDF + 資訊圖表 + 自學指南 + YouTube 網址」交給 Claude,說:
> 「依 `新增章節SOP.md` 幫我做第 N 章的教學駕駛艙並推到 ob-cockpits」,
> 它會自動完成步驟 2~7。
