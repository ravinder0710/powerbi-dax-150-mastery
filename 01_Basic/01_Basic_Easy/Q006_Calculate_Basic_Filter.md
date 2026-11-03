# Q006: Filter Modification using CALCULATE

## 📌 Metadata
- **Module:** Module 4: Filter Modification & Context
- **Tier:** 01. Basic Easy
- **Question Number:** 006 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
The business leadership needs a dedicated KPI card tracking total revenue generated exclusively by the **'Electronics'** product category, regardless of other slicer interactions or table row groupings.

Category metadata resides in `DimProduct[Category]`, while transactions are tracked in `FactSales`. A 1-to-Many active relationship connects `DimProduct` to `FactSales`.

---

## 📊 Sample Dataset

**Table: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

**Table: `FactSales`**

| OrderID | ProductID | Total Sales (from Q001) |
| :--- | :--- | :--- |
| ORD-101 | P-11 (Electronics) | 950 |
| ORD-102 | P-12 (Electronics) | 1200 |
| ORD-103 | P-11 (Electronics) | 1900 |
| ORD-104 | P-13 (Home Decor) | 450 |
| ORD-105 | P-14 (Office Supply) | 380 |
| ORD-106 | P-12 (Electronics) | 2250 |

Total Electronics Sales = $950 + 1200 + 1900 + 2250 = \mathbf{6,300}$

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

1. **Never Wrap a Measure in `SUM()`:**
   - Writing `SUM([Total Sales])` causes a syntax compilation error. `SUM()` expects a column reference (`Table[Column]`). Pre-existing measures already encapsulate their aggregation logic and must be referenced directly by name: `[Total Sales]`.

2. **Why `CALCULATE()` is the Most Powerful DAX Function:**
   - It is the **only** function in DAX capable of altering, overriding, expanding, or removing the active **Filter Context**.
   - It executes **Context Transition** (converting an existing row context into an equivalent filter context).

3. **Internal Engine Syntax Translation:**
   - When a simple Boolean filter like `DimProduct[Category] = "Electronics"` is passed to `CALCULATE()`, the DAX engine converts it under the hood to a table expression:
     ```dax
     FILTER(
         ALL(DimProduct[Category]),
         DimProduct[Category] = "Electronics"
     )
     ```
