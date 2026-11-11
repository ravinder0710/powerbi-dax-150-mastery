# Q030: Top N Portfolio Concentration % (Pareto Analysis)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 030 / 150 (Pillar 1 Grand Finale)
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive leadership and category directors monitor portfolio revenue risk using the Pareto Principle (80/20 rule):
- How much revenue is driven by the **Top 3 Best-Selling Products**?
- What percentage of the enterprise's entire product revenue is concentrated in these Top 3 items?

If 3 out of 100 products drive 90% of total revenue, the business faces acute supplier and portfolio concentration risk.

An interviewer will ask:
> *"1. How do you combine `TOPN`, `CALCULATE`, and Measure Branching to compute a Top N portfolio concentration percentage?*
> *2. Why must the denominator use `ALL(DimProduct)` instead of a simple measure reference?*
> *3. What is the impact of passing `DESC` vs `ASC` in the ranking expression?"*

---

## 📊 Sample Dataset

### `DimProduct`
| ProductKey | ProductName |
| :---: | :--- |
| `101` | Laptop *(₹ 50,000)* |
| `102` | Smartphone *(₹ 30,000)* |
| `103` | Tablet *(₹ 20,000)* |
| `104` | Mouse *(₹ 5,000)* |
| `105` | Keyboard *(₹ 5,000)* |

### `FactSales`
Total Enterprise Gross Revenue = $50\text{k} + 30\text{k} + 20\text{k} + 5\text{k} + 5\text{k} = \mathbf{₹ 1,10,000}$

---

## 🎯 Expected Output

| Metric / KPI | Logic | Expected Value | Formatted Display |
| :--- | :--- | :---: | :---: |
| **Top 3 Products Sales** | Laptop + Smartphone + Tablet | ₹ 1,00,000 | ₹ 100,000 |
| **Total Enterprise Sales** | `CALCULATE([Total Net Sales], ALL(DimProduct))` | ₹ 1,10,000 | ₹ 110,000 |
| **Top 3 Concentration %** | `₹ 100,000 / ₹ 110,000` | **0.909** | **90.9%** |

---

## 💡 Production DAX Solutions

### Measure 1: Top 3 Products Combined Revenue
```dax
Top 3 Products Sales = 
CALCULATE(
    [Total Net Sales],
    TOPN(
        3,
        VALUES(DimProduct[ProductName]),
        [Total Net Sales],
        DESC
    )
)
```

### Measure 2: Top 3 Portfolio Concentration % (Measure Branching)
```dax
Top 3 Products Sales % = 
DIVIDE(
    [Top 3 Products Sales],
    CALCULATE(
        [Total Net Sales],
        ALL(DimProduct)
    ),
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Measure Branching (`[Top 3 Products Sales]`):**
   - Reusing Measure 1 inside Measure 2 adheres to DRY (Don't Repeat Yourself) design.
   - It improves formula readability, simplifies QA reconciliation, and centralizes business logic.

2. **The Role of `ALL(DimProduct)` in the Denominator:**
   - In hierarchical or filtered visuals, the denominator must represent the grand baseline of all products regardless of visual row coordinates.
   - `ALL(DimProduct)` strips away product-level filters to ensure the concentration benchmark divides against true 100% catalog sales.

3. **`DESC` vs `ASC` Architectural Rule:**
   - Always use `DESC` for Top revenue/profit performers.
   - Use `ASC` only when specifically tracking bottom laggards, lowest turnaround times, or minimum defect rates.
