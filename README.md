# Oracle Multi · 個人化十二宮位占星週報 2026

> Personalized Weekly Astrology · 12-House Oracle for 2026 https://stella98052.github.io/oracle-multi/

本專案是一個以 **Placidus 宮位制** 為基礎的個人化十二宮位週報 Web App，支援中英雙語顯示，目標使用情境為：

- 手機為主的消費者介面（接近實際商業 App 體驗）
- 針對單一年度（目前為 2026 年）的每週個人化運勢
- 以 **十二宮位** 為核心架構的占星解讀，而非星座運勢大雜燴

This project is a bilingual (ZH/EN) web app that delivers **personalized weekly astrology reports** based on the **12-house system (Placidus)** for the year **2026**. It is designed to feel like a consumer-facing mobile app, not just a technical demo.

---

## 功能總覽 · Features

### 1. 個人化基本資料 · Personalized Birth Data

- 使用者輸入：姓名、出生日期、出生時間、城市、緯度、經度、時區與性別
- 內部計算：
  - 太陽星座（Sun sign）
  - 上升星座（Rising / Ascendant）
  - 生命靈數（Numerology life path number）
- 資料以瀏覽器 `localStorage` 儲存，在支援的環境會自動帶出上次填寫的資料（在被 sandbox 的環境中則會安全略過）

### 2. 十二宮位週運 · 12-House Weekly Forecast

- 採用 12 宮位架構，對應人生主要領域（自我、財務、家庭、事業、關係等）
- 每一宮有 1–5 顆星的整體能量評分，計算邏輯結合：
  - 個人基礎配置（BASE_SCORES）
  - 週期 Pattern（WEEK_PATTERNS）
  - 日期 hash 所產生的細微波動
  - 上升宮與與上升成 120° 的宮位會得到額外加權
- 點擊任一宮位卡片會打開 **翻牌 modal**，顯示：
  - 本週有哪些行星過境此宮（The planets transiting this house this week）
  - 每顆行星在該宮位的影響與需要注意事項（Effects & what to watch for）

### 3. 每日運勢 · Daily Outlook

- 每日顯示：
  - 日期＋星期（採較大字體，方便手機閱讀）
  - 當日月亮星座（Moon sign）
  - 一句情境式運勢說明（context sentence）
  - 一個能量標籤（如「適合行動」「適合休息」）
- Daily section uses mobile-friendly font sizes (~16px for main text) to ensure readability on phones.

### 4. 幸運元素 · Lucky Elements

- 顯示：
  - 幸運色（含顏色圓點與名稱）
  - 幸運日（Lucky day of the week）
  - 本週關鍵詞（Keyword/Focal theme）
- 個人的 **幸運數字**（Lucky numbers）因為通常不會頻繁改變，因此顯示於頂部標籤列，與太陽星座、上升星座、生日與城市並列。

### 5. 流年行星過宮 JSON · Transit JSON Integration

- 本專案使用 **`weekly_transit_signs_2026.json`** 作為 2026 年全年度的行星過境資料來源：
  - 每一週（`2026-01` ~ `2026-52`）
  - 9 顆行星：Sun, Venus, Mercury, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto
  - 每顆行星記錄其 **星座 index**（0–11）、星座名稱（中英）、行星名稱（中英）
- 前端透過：

```js
fetch('weekly_transit_signs_2026.json')
  .then(r => r.json())
  .then(data => { WEEKLY_TRANSITS = data; if (USER) renderReport(); });
```

- 函式 `getTransitingPlanets(wk, risSign, houseIdx)` 會：
  - 依週數鍵值（例如 `"2026-20"`）取得當週所有行星星座
  - 轉換為「行星過境本命第幾宮」：`(planetSign - risSign + 12) % 12`
  - 回傳落在指定宮位的行星陣列

在翻牌畫面中，這些行星會被呈現為：
- 上方的金色膠囊標籤（行星符號＋星座名稱）
- 下方的條列說明（每顆行星在該宮位的影響與注意事項）

---

## 字型與介面設計 · Typography & UI Design

### 字型 · Fonts

- 西文／數字：
  - `Cormorant Garamond`（主要正文，帶有神諭卡＋占星書氛圍）
  - `Cinzel`（標題與區塊標籤，營造古典占星感）
- 中文：
  - `Noto Serif TC`（易讀的中文襯線字型）

```css
@import url('https://fonts.googleapis.com/css2?family=Noto+Serif+TC:wght@400;500;700&family=Cinzel:wght@500;600;700&family=Cormorant+Garamond:wght@400;500;600&display=swap');

body{
  font-family: 'Cormorant Garamond','Noto Serif TC',serif;
}
```

### 手機可讀的字體尺寸 · Mobile-Friendly Font Sizes

- 主要文字（標籤與內文）約 0.95–1.05rem（約 15–17px）
- 每日運勢：
  - 日期與星期：`1rem`（約 16px）
  - 月亮星座：`1rem`
  - 每日說明文字：`1.02rem`，行距加大至 `1.5`
- 翻牌後的詳細說明：
  - 行星膠囊：`1rem`
  - 星座名稱與行星影響說明：約 `0.95rem` 以上

### 色彩與氛圍 · Colors & Mood

- 整體配色：
  - 深藍／黑夜背景
  - 金色（`--gold`）作為強調色，用於標題、邊框與關鍵 icon
  - 紫色與月光色作為輔助色，營造占星／塔羅氛圍

---

## 年度範圍限制 · Year Range Limitation (2026 Only)

目前版本**限定在 2026 年度使用**，避免使用者跳到沒有 JSON 資料的年份。程式中的做法包括：

1. **初始化 CWK（當週起算日）時的限制**：

```js
var USER=null;
var _today=new Date();
var _min=new Date('2026-01-01');
var _max=new Date('2026-12-31');
if(_today<_min)_today=_min;
if(_today>_max)_today=_max;
var CWK=getMon(_today);
```

2. **切換前一週／下一週時的限制**：

```js
function changeWeek(d){
  var dt=new Date(CWK);
  dt.setDate(dt.getDate()+d*7);
  var min=new Date('2026-01-01');
  var max=new Date('2026-12-31');
  if(dt<min)dt=min;
  if(dt>max)dt=max;
  CWK=getMon(dt);
  renderReport();
}
```

這代表：
- 使用者不論何時開啟 app，畫面永遠會停留在 2026 年的週報範圍內。
- 左右切換週數也不會超出 2026/01/01 – 2026/12/31。

若未來要支援 2027 年，只需：
- 新增一份 `weekly_transit_signs_2027.json`
- 將上述日期邊界改成 `2027-01-01` 與 `2027-12-31`
- 將 `fetch` 的目標檔名改為 2027 版本

---

## 專案架構 · Project Structure

```text
oracle-multi/
├─ index.html                   # 主 Web App（含 JS / CSS / HTML）
├─ weekly_transit_signs_2026.json  # 2026 年每週 9 顆行星星座位置
└─ README.md                    # 本說明文件
```

---

## 開發與本機執行 · Development & Local Run

### 本機啟動 · Run Locally

1. Clone 專案：

```bash
git clone https://github.com/your-account/oracle-multi.git
cd oracle-multi
```

2. 使用任意簡單靜態伺服器（例如 Python）：

```bash
python -m http.server 8000
```

3. 在瀏覽器開啟：

```text
http://localhost:8000/index.html
```

建議使用瀏覽器的「手機模擬」模式檢查字體與排版效果。

### 部署到 GitHub Pages · Deploy to GitHub Pages

1. 將 `index.html` 與 `weekly_transit_signs_2026.json` push 到 GitHub。  
2. 在 repo 設定中啟用 GitHub Pages，source 選擇 `main` branch 的根目錄。  
3. 稍待片刻，即可在分配的 URL 上訪問（例如 `https://<user>.github.io/oracle-multi/`）。

---

## 注意事項 · Disclaimers

- 本 App 以占星學與數術為靈感，用於自我覺察與生活規劃參考，**不構成任何財務、醫療或法律建議**。
- 解讀基於簡化模型（例如行星僅使用太陽、金星、水星、火星、木星、土星、天王星、海王星、冥王星，以及 Placidus 宮位制），並非完整本命盤分析。
- English messages are written for reflective and inspirational purposes only; this app does **not** guarantee outcomes.

---

## 授權與貢獻 · License & Contributions

- 若你是開發者，歡迎 fork 本專案，為不同年份新增對應的 `weekly_transit_signs_YYYY.json`。  
- 若要用於商業專案，請先檢查字型授權（Google Fonts 為 OFL / Apache 2.0）與任何你新增的內容授權狀態。

Contributions via pull requests are welcome for:
- Better copywriting (ZH/EN)
- Accessibility and performance improvements
- Additional yearly transit JSON files (with clear data sources)

