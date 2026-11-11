# Q029: Top N Aggregation (`TOPN` with `CALCULATE`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 029 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive KPI cards and summary banners frequently require displaying benchmark values such as **Top 1 Best-Selling Product Revenue** or **Top 3 Customer Concentration**:
- Rather than rendering a large table, an executive tile must dynamically calculate the total net sales of the #1 highest-revenue product within the current filter context (e.g. within a selected category or year).

The data model features:
- `DimProduct` (`ProductKey`, `ProductName`)
- `FactSales` (`OrderID`, `ProductKey`, `SalesAmount`)

An interviewer will ask:
> *"1. How does the `TOPN` table function interact with `CALCULATE` to evaluate a scalar measure?*
> *2. What happens if you specify `ASC` instead of `DESC` as the order parameter in `TOPN`?*
> *3. What happens if two products tie for the #1 position?"*

---

## 📊 Sample Dataset

### `DimProduct`
| ProductKey | ProductName |
| :---: | :--- |
| `101` | iPhone 15 |
| `102` | Samsung S24 |
| `103` | OnePlus 12 |

### `FactSales`
| OrderID | ProductKey | SalesAmount |
| :---: | :---: | :---: |
| `1` | 101 *(iPhone 15)* | ₹ 90,000 |
| `2` | 102 *(Samsung S24)* | ₹ 70,000 |
| `3` | 103 *(OnePlus 12)* | ₹ 40,000 |

---

## 🎯 Expected Output

| Metric / Scenario | Sorted Order | Target Product | Expected Value |
| :--- | :---: | :--- | :---: |
| **Top 1 Best-Seller** | `DESC` (Descending) | iPhone 15 | **₹ 90,000** |
| **Bottom 1 Worst-Seller** | `ASC` (Ascending) | OnePlus 12 | **₹ 40,000** |

---

## 💡 Production DAX Solutions

### Top 1 Best-Seller Production Measure:
```dax
Top 1 Product Sales = 
CALCULATE(
    [Total Net Sales],
    TOPN(
        1,
        VALUES(DimProduct[ProductName]),
        [Total Net Sales],
        DESC
    )
)
```

### Bottom 1 Worst-Seller Production Measure:
```dax
Bottom 1 Product Sales = 
CALCULATE(
    [Total Net Sales],
    TOPN(
        1,
        VALUES(DimProduct[ProductName]),
        [Total Net Sales],
        ASC
    )
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `TOPN` Works with `CALCULATE`:**
   - `TOPN` is a **Table Function**, not a scalar function. It returns a subset table containing the top N rows based on the evaluation of `[Total Net Sales]`.
   - When passed into `CALCULATE`'s filter argument, `CALCULATE` uses this 1-row table as a filter context override, effectively computing `[Total Net Sales]` solely for that top product.

2. **The `ASC` vs `DESC` Interview Trap:**
   - `DESC` (Descending): Highest to lowest $\rightarrow$ Used for **Top Performers**.
   - `ASC` (Ascending): Lowest to highest $\rightarrow$ Used for **Bottom / Worst Performers**.

3. **Tie-Breaker Rule (Edge Case):**
   - If two products have the exact same highest revenue (e.g., both tied at ₹ 90,000), `TOPN(1, ...)` returns **both rows** (2 rows instead of 1).
   - If strict single-row behavior is required, add a secondary tie-breaker expression to `TOPN`:
     `TOPN(1, VALUES(DimProduct[ProductName]), [Total Net Sales], DESC, DimProduct[ProductName], ASC)`
