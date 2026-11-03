# Q009: Removing Filters using ALL (Grand Total Calculation)

## 📌 Metadata
- **Module:** Module 4: Filter Modification & Context
- **Tier:** 01. Basic Easy
- **Question Number:** 009 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
In an executive category breakdown visual, business stakeholders require a side-by-side comparison between the current row's category sales and the overall **Grand Total Sales across all products**.

In standard Power BI visuals, each row evaluates under its own Filter Context (e.g., Row 1 is filtered to `Category = "Electronics"`). To calculate a fixed Grand Total, the DAX measure must explicitly **remove/clear** the active Category filter using the `ALL()` function inside `CALCULATE()`.

---

## 📊 Sample Dataset

**Table: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

Category Sales Totals:
- Electronics = ₹6,300
- Home Decor = ₹450
- Office Supply = ₹380
- **Overall Grand Total = ₹7,130**

---

## 🎯 Expected Output

| Category | Total Sales (Q001) | All Products Sales (New Measure) |
| :--- | :--- | :--- |
| Electronics | 6,300 | **7,130** |
| Home Decor | 450 | **7,130** |
| Office Supply | 380 | **7,130** |
| **Total** | **7,130** | **7,130** |

---

## 💡 Production DAX Solutions

### Solution 1: Specific Column Filter Removal
```dax
All Products Sales = 
CALCULATE(
    [Total Sales],
    ALL(DimProduct[Category])
)
```

### Solution 2: Full Table Filter Removal (Absolute Total)
```dax
All Products Sales = 
CALCULATE(
    [Total Sales],
    ALL(DimProduct)
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `ALL()` Operates inside `CALCULATE()`:**
   - When passed as a filter argument to `CALCULATE()`, `ALL()` acts as a **filter removal modifier**. It instructs the engine to ignore existing filter context on the target column or table.

2. **`ALL(Column)` vs `ALL(Table)`:**
   - **`ALL(Table[Column])`**: Removes filters from that single column only. If the report user filters by another column in `DimProduct` (such as `SubCategory` or `Color`), those filters remain active.
   - **`ALL(Table)`**: Removes all filters from every column across the entire dimension table, guaranteeing an absolute grand total regardless of which dimension attributes are sliced.
