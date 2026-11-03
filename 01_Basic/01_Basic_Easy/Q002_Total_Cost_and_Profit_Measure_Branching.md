# Q002: Total Cost and Profit using Measure Branching

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 002 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
Following the creation of `[Total Sales]`, the finance department requires visibility into the company's cost of goods sold (COGS) and net profitability per order.
- Each order line has a `Quantity` and a `UnitCost`. Total cost must account for all units sold.
- Profit must be computed by leveraging existing measures (**Measure Branching**) rather than writing monolithic formulas.

Formulas:
$$\text{Total Cost} = \text{Quantity} \times \text{UnitCost}$$
$$\text{Total Profit} = \text{Total Sales} - \text{Total Cost}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount | UnitCost |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 | 300 |
| ORD-102 | P-12 | 1 | 1200 | 0 | 800 |
| ORD-103 | P-11 | 4 | 500 | 100 | 300 |
| ORD-104 | P-13 | 3 | 150 | 0 | 90 |
| ORD-105 | P-14 | 5 | 80 | 20 | 50 |
| ORD-106 | P-12 | 2 | 1200 | 150 | 800 |

*(Pre-requisite Measure available: `[Total Sales]`)*

---

## 🎯 Expected Output

| OrderID | Total Sales | Total Cost | Total Profit |
| :--- | :--- | :--- | :--- |
| ORD-101 | 950 | 600 | 350 |
| ORD-102 | 1200 | 800 | 400 |
| ORD-103 | 1900 | 1200 | 700 |
| ORD-104 | 450 | 270 | 180 |
| ORD-105 | 380 | 250 | 130 |
| ORD-106 | 2250 | 1600 | 650 |
| **Total** | **7130** | **4720** | **2410** |

---

## 💡 Production DAX Solution

```dax
-- Measure 1: Total Cost
Total Cost = 
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitCost]
)

-- Measure 2: Total Profit (Leveraging Measure Branching)
Total Profit = 
[Total Sales] - [Total Cost]
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why not `SUM(FactSales[UnitCost])`?**
   - Simple `SUM` aggregates the `UnitCost` column directly without factoring in `Quantity`. When an order contains multiple units (e.g., 2 units at $300 each), `SUM` yields $300 instead of the true total cost of $600. `SUMX` iterates over each row to multiply `Quantity * UnitCost` before summing.

2. **What is Measure Branching?**
   - Measure Branching refers to the practice of chaining smaller, modular measures together (e.g., combining `[Total Sales]` and `[Total Cost]` into `[Total Profit]`).

3. **Advantages of Measure Branching in Enterprise BI:**
   - **Maintainability & DRY:** If discount or tax logic changes inside `[Total Sales]`, all downstream calculations (`Profit`, `Margin %`) update automatically without code changes.
   - **Engine Optimization:** Power BI's Formula Engine can reuse cached query sub-plans for base measures during visual rendering.
