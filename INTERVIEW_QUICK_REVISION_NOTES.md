# 🎯 DAX Interview Quick Revision Notes (Top 150 Mastery)
> **Purpose:** Interview se 15 minute pehle revise karne ke liye crisp Hinglish notes, interviewer punchlines, aur traps.

---

## 📌 Q001: Row-by-Row Iterator (`SUMX`) vs Simple Aggregation (`SUM`)
* **Formula:**
  ```dax
  Total Net Sales = SUMX(FactSales, (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount])
  ```
* **Business Problem:** Client data warehouse me `SalesAmount` ka column store nahi karta taaki storage bache. Hume on-the-fly net revenue nikalna hai.
* **SUM kyu fail hota hai?** `SUM()` sirf ek single column leta hai (e.g. `SUM(Table[Col])`). Ye do ya teen columns ke beech calculation (`Qty * Price - Discount`) evaluate nahi kar sakta.
* **SUMX kyu lagaya?** `SUMX` ek iterator hai jo table par **Row Context** banata hai, row-by-row calculation karta hai, aur end me Filter Context ke according SUM deta hai.
* **Calculated Column kyu nahi banaya?** Calculated column RAM aur disk space permanently gher leta hai. Measure dynamically compute hota hai aur model ko fast aur light rakhta hai.
* **Syntax Rule:** Column ke aage table ka naam hamesha aayega (`FactSales[Quantity]`), measure hamesha akela aayega (`[Total Net Sales]`).

---

## 📌 Q002: Measure Branching (`Total Cost` & `Total Profit`)
* **Formulas:**
  ```dax
  Total Cost = SUMX(FactSales, FactSales[Quantity] * FactSales[UnitCost])
  Total Profit = [Total Net Sales] - [Total Cost]
  ```
* **Business Problem:** Company ko apna COGS (Cost of Goods Sold) aur actual Gross Profit track karna hai.
* **Measure Branching ka fayda?** 
  1. **DRY Principle (Don't Repeat Yourself):** Kal ko agar sales ya cost ka logic badla, to sirf base measure badalna padega; profit apne aap update ho jayega.
  2. **Formula Engine Cache:** Power BI intermediate measures ke results ko cache kar leta hai jisse visuals fast load hote hain.
  3. **Clean Code:** 15 line ka monster code likhne se bachate hain.

---

## 📌 Q003: Safe Division (`DIVIDE` vs `/`)
* **Formula:**
  ```dax
  Profit Margin % = DIVIDE([Total Profit], [Total Net Sales], 0)
  ```
* **Business Problem:** Percentage margin dekhna zaroori hai taaki alag-alag ticket size ke products ki profitability compare ki ja sake.
* **DIVIDE kyu lagaya?** Standard `/` operator division-by-zero par `Infinity` ya `NaN` error deta hai aur visual toot jata hai. `DIVIDE()` safe-division karta hai.
* **3rd parameter na de to?** Default `BLANK()` return hota hai.
* **Advance Trap:** `BLANK()` chhodne se visual empty rows ko auto-suppress (hide) kar deta hai, jisse report fast chalti hai. `0` dene par Power BI zero wali sari rows render karta hai.

---

## 📌 Q004: Distinct Counting (`DISTINCTCOUNT` vs `COUNTROWS` vs `COUNT`)
* **Formula:**
  ```dax
  Total Orders Placed = DISTINCTCOUNT(FactSales[OrderID])
  ```
* **Business Problem:** E-commerce me ek order me multiple items hone par duplicate rows hoti hain. `COUNTROWS` lene se order count galat (inflated) ho jayega.
* **Blank Trap:** `DISTINCTCOUNT` blank value ko bhi count (+1) kar leta hai. Blank ignore karne ke liye **`DISTINCTCOUNTNOBLANK(Column)`** use karte hain.
* **COUNTROWS vs COUNT:** `COUNTROWS(Table)` hamesha fast hota hai kyunki ye VertiPaq metadata se direct total rows utha leta hai bina data page scan kiye.


---

## 📌 Q005: Average Order Value (AOV) & Basket Size (UPT)
* **Formulas:**
  ```dax
  Average Order Value = DIVIDE([Total Net Sales], [Total Orders Placed], 0)
  Units Per Transaction = DIVIDE(SUM(FactSales[Quantity]), [Total Orders Placed], 0)
  ```
* **Business Problem:** Retail business ko customer ka buying pattern samajhna hota hai—har order par kitne rupaye ka transaction hua aur average kitni items khareedi gayi.
* **Direct AVERAGE() kyu fail hota hai?**
  1. **Syntax Rule:** DAX me `AVERAGE([Measure])` syntax error deta hai. Wo sirf physical column leta hai.
  2. **Grain Mismatch:** Fact table ki rows ko average karne se **Line-Item Average** nikalta hai, **Order-level Average** nahi! Kyunki 1 order me multiple items hote hain. Sahi formula hamesha `Total Sales / Total Orders` hota hai.


---

## 📌 Q006: Filter Modification (`CALCULATE`)
* **Formula:**
  ```dax
  Electronics Net Sales = CALCULATE([Total Net Sales], DimProduct[Category] = "Electronics")
  ```
* **Business Problem:** Hardcoded category KPIs freeze karna overview dashboard ke liye.
* **CALCULATE kyu Super-Power hai?**
  1. Pooray DAX me **akela function** hai jo Filter Context ko modify/add/overwrite/delete kar sakta hai.
  2. Ye **Context Transition** perform karta hai (Row Context ko Filter Context me badal deta hai).
  3. Internal Engine execution: Boolean condition `Category = "Electronics"` background me `FILTER(ALL(DimProduct[Category]), DimProduct[Category] = "Electronics")` me convert hoti hai.

---

## 📌 Q007: Multiple Filters in CALCULATE (Comma `,` vs `&&`)
* **Formula:**
  ```dax
  North Electronics Sales = CALCULATE([Total Net Sales], DimProduct[Category] = "Electronics", DimStore[Region] = "North")
  ```
* **Comma `,` vs `&&` Difference:**
  - **Comma `,` Best Practice hai:** VertiPaq Storage Engine (SE) dono filters ko parallel scan karta hai aur bitmap index ka direct intersection leta hai (super-fast).
  - **`&&` across tables:** Formula Engine (FE) ko memory me materialization ke liye force kar sakta hai jo slow hota hai.

---

## 📌 Q008: Logical OR & The IN Operator (`IN` vs `||`)
* **Formula:**
  ```dax
  Electronics & Home Decor Sales = CALCULATE([Total Net Sales], DimProduct[Category] IN { "Electronics", "Home Decor" })
  ```
* **Curly Braces `{}` kyu lagte hain?** DAX me `{}` **Table Constructor** hota hai jo 1-column temporary table banata hai. `IN` is table ke sath semi-join karta hai.
* **`IN` vs `||`:** Chained `||` slow hota hai; `IN` ko engine optimized Hash Match me chalata hai.

---

## 📌 Q009: Removing Filters using ALL (`ALL(Column)` vs `ALL(Table)`)
* **Formula:**
  ```dax
  All Products Sales = CALCULATE([Total Net Sales], ALL(DimProduct))
  ```
* **Business Problem:** Company ka overall benchmark total nikalna taaki har category apni performance compare kar sake.
* **ALL(Column) vs ALL(Table) Trap:**
  - `ALL(Column)` sirf us column ka filter hatata hai (doosre column ka slicer lagane par measure filter ho jayega).
  - `ALL(Table)` puri table ke saare columns ke filters clear kar deta hai.

---

## 📌 Q010: Contribution % of Total Sales (`% of Total`)
* **Formula:**
  ```dax
  % of Total Sales = DIVIDE([Total Net Sales], CALCULATE([Total Net Sales], ALL(DimProduct)), 0)
  ```
* **Business Problem:** Pareto 80/20 analysis—kaun si category business ka sabse bada revenue share drive karti hai.
* **Numerator vs Denominator:** Numerator row ke filter context par calculate hota hai; denominator me `ALL()` se filter hatakar grand total nikalta hai.
