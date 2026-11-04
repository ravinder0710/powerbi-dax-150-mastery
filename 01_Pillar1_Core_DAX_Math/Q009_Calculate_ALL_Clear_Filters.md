# Q009: Removing Filters using ALL (Grand Total Calculation)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 009 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive stakeholders require a side-by-side comparison between individual category net sales and overall enterprise **Grand Total Net Sales across all products**.

In standard matrix visuals, each row evaluates under its own Filter Context (e.g. `Category = "Electronics"`). To compute a fixed benchmark Grand Total, the DAX measure must explicitly remove the active category filter using `ALL()` inside `CALCULATE()`.

---

## 📊 Sample Dataset

**Table: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

Category Totals:
- Electronics = ₹ 6,300
- Home Decor = ₹ 450
- Office Supply = ₹ 380
- **Company Grand Total = ₹ 7,130**

---

## 🎯 Expected Output

| Category | Total Net Sales | All Products Sales (New Measure) |
| :--- | :--- | :--- |
| Electronics | ₹ 6,300 | **₹ 7,130** |
| Home Decor | ₹ 450 | **₹ 7,130** |
| Office Supply | ₹ 380 | **₹ 7,130** |
| **Total** | **₹ 7,130** | **₹ 7,130** |

---

## 💡 Production DAX Solution

```dax
All Products Sales = 
CALCULATE(
    [Total Net Sales],
    ALL(DimProduct)
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `ALL()` Operates:**
   - When passed as a filter argument in `CALCULATE()`, `ALL()` acts as a filter removal modifier, clearing active filters on the target table or column.
2. **`ALL(Column)` vs `ALL(Table)`:**
   - `ALL(DimProduct[Category])` clears filters from `Category` only. If other columns like `Color` or `SubCategory` are sliced, they continue to filter the measure.
   - `ALL(DimProduct)` clears all filters from the entire dimension table, guaranteeing an absolute grand total.
