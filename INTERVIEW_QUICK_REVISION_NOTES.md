# 🎯 DAX Interview Quick Revision Notes (Zero to Hero)
> **Purpose:** Crisp, one-liner answers, interview defense traps, and formula references to review before any technical interview.

---

## 📌 Q001: Row-by-Row Iterator (`SUMX`) vs Aggregation (`SUM`)
* **Core Formula:**
  ```dax
  Total Sales = SUMX(FactSales, (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount])
  ```
* **Why not `SUM`?** `SUM` only accepts a single column (`SUM(Table[Column])`). To compute an arithmetic expression across multiple columns row-by-row, an iterator (`SUMX`) is mandatory.
* **Why not a Calculated Column?** Calculated columns consume permanent RAM & storage in the VertiPaq engine. Measures compute dynamically on-the-fly and save memory.
* **Naming Standard Rule:** Columns MUST have table prefix (`FactSales[Quantity]`), measures MUST be standalone (`[Total Sales]`).

---

## 📌 Q002: Measure Branching (Reusing Base Measures)
* **Core Formulas:**
  ```dax
  Total Cost = SUMX(FactSales, FactSales[Quantity] * FactSales[UnitCost])
  Total Profit = [Total Sales] - [Total Cost]
  ```
* **Why Measure Branching?** 
  1. **DRY Principle (Don't Repeat Yourself):** Changing discount logic in `[Total Sales]` automatically cascades updates to Profit and Margin without touching other measures.
  2. **Engine Cache:** Power BI Formula Engine caches intermediate measure results across visual elements.

---

## 📌 Q003: Safe Division (`DIVIDE` vs `/`)
* **Core Formula:**
  ```dax
  Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
  ```
* **Why `DIVIDE` over `/`?** `/` crashes or returns `Infinity` / `NaN` when the denominator is 0. `DIVIDE` handles division by zero internally.
* **What if 3rd parameter is omitted?** Returns `BLANK()`. This is often preferred because Power BI tables automatically suppress blank rows, optimizing visual performance.

---

## 📌 Q004: Distinct Counting (`DISTINCTCOUNT` vs `DISTINCTCOUNTNOBLANK` vs `COUNTROWS`)
* **Core Formula:**
  ```dax
  Total Orders Placed = DISTINCTCOUNT(FactSales[OrderID])
  ```
* **The Blank Trap:** `DISTINCTCOUNT()` counts `BLANK()` as a valid unique category (adds +1). To exclude blanks, use `DISTINCTCOUNTNOBLANK()`.
* **Counting Table Rows:** Always use `COUNTROWS(Table)` instead of `COUNT(Column)`. `COUNTROWS` reads metadata directly from VertiPaq without scanning column pages, making it much faster.

---

## 📌 Q005: Average Order Value (AOV) & Ratio Metrics
* **Core Formula:**
  ```dax
  Average Order Value = DIVIDE([Total Sales], [Total Orders Placed], 0)
  ```
* **Why does `AVERAGE()` fail here?**
  1. `AVERAGE()` requires a physical column reference; passing a dynamic measure `AVERAGE([Total Sales])` is a syntax error.
  2. In transactional fact tables with multiple line items per order, averaging rows yields **Line-Item Average**, not **Order Average**.

---

## 📌 Q006: Filter Modification (`CALCULATE`)
* **Core Formula:**
  ```dax
  Electronics Sales = CALCULATE([Total Sales], DimProduct[Category] = "Electronics")
  ```
* **The Syntax Trap:** Never wrap a measure inside `SUM()` (e.g. `SUM([Total Sales])` is illegal). Reference measures directly.
* **Why `CALCULATE` is the Super-Power Function?** It is the **only** function in DAX that can modify the Filter Context (add, overwrite, or remove filters) and trigger Context Transition.
* **Relationship Propagation:** Filters placed on a Dimension table (`DimProduct`) automatically filter the Fact table (`FactSales`) via active 1-to-many relationships.
