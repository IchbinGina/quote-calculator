---
name: quote-estimate
description: 瘋戶外團建活動報價試算。使用者給活動、人數、時數（半天/全天）、分組或毛利率，要算成本、報價、每人價，或寫進提案信／報價單前需要金額時使用。
---

# 瘋戶外報價試算

## 1. 取得成本參數（唯一來源）
- 先用 WebFetch 讀 `https://ichbingina.github.io/quote-calculator/costs.json`，prompt 寫「原樣輸出完整 JSON，不要摘要」。
- 讀不到時改用專案記憶 `pricing-and-staffing.md`，並在回覆中註明「成本表未讀到最新 costs.json」。
- 計算機（報價計算機.html）讀同一份檔案，兩邊數字必須一致；不要自行改動參數。

## 2. 成本公式（k = half 半天 3hr / full 全天 6hr）
- 組數 = ceil(人數 ÷ 每組人數)；每組人數取 `products[活動].groupSize`，無則 `staffDefault.groupSize`（使用者指定組數時以使用者為準）
- 關主人數 = ceil(組數 ÷ groupsPerStaff)；關主費 = 人數 × staffRate[k]
- 工作人員數 = 講師1 + 關主 + 行政1 + 窗口1
- 成本 = 講師 instructor[k] + 關主費 + 行政 admin[k] + 窗口 + 交通 + 保險（(學員+工作人員)×insurancePerPerson）+ 名牌（學員×badgePerPax）+ 客製 + 雜支
- `_說明` 註記保險另計的活動（自力造筏、神鬼奇航水版），要提醒使用者。

## 3. 報價
- 一律未稅；毛利率以未稅為分母：報價 = 成本 ÷ (1 − 毛利率)
- 未指定毛利率時用 `margin.default`（60%），並列出 tiers 各檔
- 總價依 `rounding.total` 四捨五入；每人價依 `rounding.perPax`
- 含稅 = 未稅 × (1 + taxRate)，僅供參考

## 4. 參考價檢查（必做）
- 依人數找 `refPricing[活動]` 區間；lumpMin/lumpMax 為整體報價
- 報價低於參考價下限：先列出差距並詢問使用者，取得同意前不得寫進信件或報價單
- 使用者直接給金額時：以使用者金額為準，只回報毛利率與是否在參考區間

## 5. 輸出格式（精簡）
- 成本明細表（項目｜金額｜說明）→ 報價（未稅、含稅、每人未稅）→ 毛利率 → 參考區間判斷
- 若報價與同一客戶過去報價不同，提醒差異
