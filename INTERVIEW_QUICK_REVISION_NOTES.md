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
