# 教學駕駛艙 ‧ Interactive Teaching Cockpits

黑板粉筆風格的互動教學駕駛艙合集,以 GitHub Pages 部署,提供學生線上自學與課堂教學使用。

每個單元整合:
- 🖼️ NotebookLM 簡報輪播
- 📺 教學影片(YouTube,可在頁面內就地播放)
- 📊 資訊圖表
- 🕹️ 互動視覺化
- 📝 形成性評量(即時批改)
- 🛠️ 課堂工具:抽籤點名、計時器、粉筆畫筆
- 👩‍🏫 教師 / 🧑‍🎓 學生模式切換

## 線上網址

部署後的入口頁(總覽):`https://<你的帳號>.github.io/ob-cockpits/`

| 單元 | 連結 |
| --- | --- |
| 組織行為學 ‧ 第一章 | `CH1/` |

## 目錄結構

```
ob-cockpits/
├── index.html          # 總覽入口頁(列出所有駕駛艙)
├── .nojekyll           # 關閉 Jekyll，確保檔案原樣提供
├── CH1/                # 第一章教學駕駛艙
│   ├── index.html
│   ├── slides/         # 15 張簡報圖
│   └── Ch1_資訊圖表*.png
└── …                   # 日後的 CH2、CH3 等
```

## 新增一個駕駛艙

詳細步驟見 **[新增章節SOP.md](新增章節SOP.md)**。摘要:

1. 新增資料夾 `CHx/`,放入該單元的 `index.html` 與素材。
2. 在根目錄 `index.html` 複製一張 `<a class="card">` 卡片,改連結與標題。
3. commit、push,GitHub Pages 會自動更新。

## 教學影片設定

每個駕駛艙的 `index.html` 內有 `VIDEOS` 設定區,填入各教學重點的 YouTube 網址即可;
教師也可在頁面「📺 教學影片」分頁直接貼上網址(記憶於瀏覽器)。
