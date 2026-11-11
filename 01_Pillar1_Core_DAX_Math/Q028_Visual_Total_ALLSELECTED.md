# Q028: Visual Sliced Total vs Grand Total (`ALL` vs `ALLSELECTED`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 028 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
A category performance report contains an external page-level slicer allowing users to filter by selected product categories (e.g., selecting only `Electronics` and `Furniture`, while excluding `Clothing`).

Inside the visual (Matrix or Bar Chart), business users expect the percentage contribution of displayed categories to sum up to **exactly 100%**:
- If `ALL(FactSales)` is used, the denominator evaluates against the entire enterprise database (including unselected Clothing), causing the visible categories to sum to only **75%** (a frequent source of client confusion).
- The denominator must honor external slicers while clearing internal visual row coordinates.

An interviewer will ask:
> *"1. What is the fundamental difference between `ALL` and `ALLSELECTED`?*
> *2. Why does `ALL` cause visual percentages to not sum to 100% when slicers are applied?*
> *3. What arguments does `ALLSELECTED` accept?"*

---

## 📊 Sample Dataset

### `DimProduct`
| ProductKey | Category |
| :---: | :--- |
| `1` | Electronics *(Selected in Slicer ✅)* |
| `2` | Furniture *(Selected in Slicer ✅)* |
| `3` | Clothing *(Excluded by Slicer ❌)* |

### `FactSales`
| OrderID | ProductKey | SalesAmount |
| :---: | :---: | :---: |
| `101` | 1 *(Electronics)* | ₹ 60,000 |
| `102` | 2 *(Furniture)* | ₹ 40,000 |
| `103` | 3 *(Clothing)* | ₹ 33,333 |

---

## 🎯 Expected Output (Slicer: Electronics & Furniture)

| Category | Sales | `ALL` Denominator (Wrong ❌) | `ALLSELECTED` Denominator (Production ✅) |
| :--- | :---: | :---: | :---: |
| Electronics | ₹ 60,000 | 45.0% ($\frac{60k}{133.3k}$) | **60.0%** ($\frac{60k}{100k}$) |
| Furniture | ₹ 40,000 | 30.0% ($\frac{40k}{133.3k}$) | **40.0%** ($\frac{40k}{100k}$) |
| **Total** | **₹ 1,00,000** | **75.0%** *(Bug)* | **100.0%** *(Correct)* |

---

## 💡 Production DAX Solutions

### Standard Production Pattern (Empty Argument):
```dax
% of Selected Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE(
        [Total Net Sales],
        ALLSELECTED()
    ),
    0
)
```

### Explicit Column Pattern:
```dax
% of Selected Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE(
        [Total Net Sales],
        ALLSELECTED(DimProduct[Category])
    ),
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **`ALL` vs `ALLSELECTED` (The Golden Interview Rule):**
   - **`ALL()`**: Completely strips away all filters, including external slicers and visual row/column headers. Returns the grand total of the underlying dataset.
   - **`ALLSELECTED()`**: Restores the filter context to the outer context of the visual query. It ignores the visual's internal grouping (e.g. Category rows) while **preserving external slicers and page filters**.

2. **Syntax Trap (`ALLEXCEPT` vs `ALLSELECTED`):**
   - `ALLEXCEPT(TableName, Col1, Col2)` accepts a table followed by protected columns.
   - `ALLSELECTED()` accepts either:
     - No arguments: `ALLSELECTED()` (resets all visual groupings).
     - A specific table: `ALLSELECTED(DimProduct)`.
     - One or more columns: `ALLSELECTED(DimProduct[Category])`.
   - Passing `ALLSELECTED(Table, Column)` together throws a DAX syntax error.
