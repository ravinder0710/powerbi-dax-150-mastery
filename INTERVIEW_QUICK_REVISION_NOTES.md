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
