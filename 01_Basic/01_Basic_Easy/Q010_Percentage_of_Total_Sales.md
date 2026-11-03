# Q010: Percentage of Total (Contribution Ratio)

## 📌 Metadata
- **Module:** Module 4: Filter Modification & Context
- **Tier:** 01. Basic Easy
- **Question Number:** 010 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
Executive management requires category contribution analysis to identify which product categories drive the largest share of total business revenue.

The visual must compute each category's sales contribution percentage dynamically against the entire business grand total:
$$\text{\% of Total Sales} = \frac{\text{Current Category Sales}}{\text{Grand Total Sales}}$$

---

## 📊 Sample Dataset

**Table: `DimProduct`**

| Category | Total Sales (Q001) | All Products Sales (Q009) | Expected Contribution % |
| :--- | :--- | :--- | :--- |
| **Electronics** | ₹ 6,300 | ₹ 7,130 | **88.36%** |
| **Home Decor** | ₹ 450 | ₹ 7,130 | **6.31%** |
| **Office Supply** | ₹ 380 | ₹ 7,130 | **5.33%** |
| **Total** | **₹ 7,130** | **₹ 7,130** | **100.00%** |

---

## 🎯 Expected Output

| Category | Total Sales | % of Total Sales |
| :--- | :--- | :--- |
| Electronics | ₹ 6,300 | 88.36% |
| Home Decor | ₹ 450 | 6.31% |
| Office Supply | ₹ 380 | 5.33% |
| **Total** | **₹ 7,130** | **100.00%** |

---

## 💡 Production DAX Solutions

### Approach 1: Measure Branching (Clean & Modular)
```dax
% of Total Sales = 
DIVIDE(
    [Total Sales],
    [All Products Sales],
    0
)
```

### Approach 2: Monolithic Standalone Measure (Direct Engine Calculation)
```dax
% of Total Sales = 
DIVIDE(
    [Total Sales],
    CALCULATE([Total Sales], ALL(DimProduct)),
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How Filter Context Operates on the Numerator vs Denominator:**
   - **Numerator (`[Total Sales]`):** Evaluated under the row's incoming Filter Context (e.g. `Category = "Electronics"` yields ₹6,300).
   - **Denominator (`CALCULATE([Total Sales], ALL(DimProduct))`):** `ALL()` clears the category filter, forcing the denominator to evaluate the grand total across all products (₹7,130).

2. **Safe Division:**
   - Always wrap contribution metrics inside `DIVIDE()` to protect against division-by-zero errors when overall sales are zero or blank.
