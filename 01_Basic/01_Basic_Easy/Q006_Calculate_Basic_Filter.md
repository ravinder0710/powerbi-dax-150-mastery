# Q006: Filter Context Modification using CALCULATE

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators / Basic CALCULATE
- **Tier:** 01. Basic Easy
- **Question Number:** 006 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
The commercial reporting team needs a standalone KPI card that isolates and displays **Total Sales for the 'Electronics' Category**, irrespective of page slicers or table visuals. 

The `Category` attribute exists in the dimension table (`DimProduct`), while sales transactions exist in `FactSales`. The two tables are connected via a 1-to-Many relationship on `ProductID`.

Formula:
$$\text{Electronics Sales} = \text{Total Sales where DimProduct[Category] = "Electronics"}$$

---

## 📊 Sample Dataset

**`DimProduct`**
| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

**`FactSales`**
| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount | Total Sales |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 | 950 |
| ORD-102 | P-12 | 1 | 1200 | 0 | 1200 |
| ORD-103 | P-11 | 4 | 500 | 100 | 1900 |
| ORD-104 | P-13 | 3 | 150 | 0 | 450 |
| ORD-105 | P-14 | 5 | 80 | 20 | 380 |
| ORD-106 | P-12 | 2 | 1200 | 150 | 2250 |

Electronics Total: $950 + 1200 + 1900 + 2250 = \mathbf{6,300}$

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│    Electronics Sales    │
│                         │
│         ₹ 6,300         │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Electronics Sales = 
CALCULATE(
    [Total Sales],
    DimProduct[Category] = "Electronics"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Common Trap — Wrapping Measures in `SUM()`:**
   - Candidate mistake: Writing `CALCULATE(SUM(FactSales[Total Sales]), ...)`.
   - `[Total Sales]` is a measure. Measures cannot be passed into scalar aggregation functions like `SUM()`. Measures must be passed directly into `CALCULATE()`.

2. **Why is `CALCULATE()` the most important DAX function?**
   - It is the **only** function in DAX that can modify the incoming Filter Context (add, overwrite, or clear filters).
   - It automatically propagates filters across active 1-to-Many relationships from Dimension tables down to Fact tables.
   - It triggers **Context Transition** (converting row context into filter context).
