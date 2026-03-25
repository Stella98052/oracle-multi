# 🔮 個人化占星週運預測系統（多人版）

> 星盤來源：[Astro-Seek.com](https://astro.com) ｜ 占星解讀依據：Susan Miller 方法論

## 功能特色

- 輸入任意出生資料，即時產生個人化本週星盤預測
- 支援全球城市座標（或自訂經緯度）
- 8大領域×7天熱力圖（運勢/健康/家庭/學業/工作/財務/交通/人際）
- 個人化幸運色（依太陽星座守護星標記最吉日⭐）
- 回歸校正模型 R²=0.85，8年數據驗證

## 網址

https://Stella98052.github.io/oracle-multi

## 技術說明

- 純靜態 HTML + JavaScript，無後端
- 天文計算：Jean Meeus 演算法（精度 ±1°）
- 宮位系統：Whole Sign Houses
- 時區：自動處理 UTC 轉換

## 分享連結格式

```
https://[帳號].github.io/oracle-multi/?n=姓名&d=YYYY-MM-DD&t=HH:MM&c=緯度,經度&w=週起始日
```

範例：
```
https://[帳號].github.io/oracle-multi/?n=LA+LA&d=1970-04-28&t=13:30&c=25.05,121.53&w=2026-03-22
```

## 每月更新方式

```bash
git add index.html
git commit -m "月度模型更新 YYYY-MM"
git push
```

## 免責聲明

本系統謹供個人研究開發，請參考使用，不具備任何決策建議。

---
*系統版本 v2.0 · 2026-03-25*
