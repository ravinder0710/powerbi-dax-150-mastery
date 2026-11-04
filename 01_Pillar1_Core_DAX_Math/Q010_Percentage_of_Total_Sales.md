# Q010: Percentage of Total (Contribution Ratio)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 010 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive commercial leadership requires portfolio contribution analysis (Pareto Analysis) to identify which categories generate the dominant share of enterprise sales.

$$\text{\% of Total Sales} = \frac{\text{Current Category Sales}}{\text{Grand Total Sales}}$$

---

## 📊 Sample Dataset

**Table: `DimProduct`**

| Category | Total Net Sales (Q001) | All Products Sales (Q009) | Expected Share % |
| :--- | :--- | :--- | :--- |
| **Electronics** | ₹ 6,300 | ₹ 7,130 | **88.36%** |
| **Home Decor** | ₹ 450 | ₹ 7,130 | **6.31%** |
| **Office Supply** | ₹ 380 | ₹ 7,130 | **5.33%** |
| **Total** | **₹ 7,130** | **₹ 7,130** | **100.00%** |

---

## 🎯 Expected Output

| Category | Total Net Sales | % of Total Sales |
| :--- | :--- | :--- |
| Electronics | ₹ 6,300 | 88.36% |
| Home Decor | ₹ 450 | 6.31% |
| Office Supply | ₹ 380 | 5.33% |
| **Total** | **₹ 7,130** | **100.00%** |

---

## 💡 Production DAX Solutions

### Approach 1: Measure Branching (Modular & Clean)
```dax
% of Total Sales = 
DIVIDE(
    [Total Net Sales],
    [All Products Sales],
    0
)
```

### Approach 2: Direct Single Measure (Interviewer Standard)
```dax
% of Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE([Total Net Sales], ALL(DimProduct)),
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Filter Context Interaction:**
   - **Numerator (`[Total Net Sales]`):** Evaluates under the incoming row filter context (e.g. `Category = "Electronics"` produces ₹ 6,300).
   - **Denominator (`CALCULATE([Total Net Sales], ALL(DimProduct))`):** `ALL()` clears the category filter, forcing evaluation across the entire company total (₹ 7,130).
2. **Safe Division:**
   - `DIVIDE()` protects against divide-by-zero exceptions if net sales are zero or blank.
