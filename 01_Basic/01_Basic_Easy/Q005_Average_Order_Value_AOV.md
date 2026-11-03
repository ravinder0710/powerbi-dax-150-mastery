# Q005: Average Order Value (AOV) & Ratio Metrics

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 005 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
The business management wants to monitor the financial health of the retail business through key ratio metrics:
1. **Average Order Value (AOV):** The average monetary value generated per distinct customer order.

Formula:
$$\text{AOV} = \frac{\text{Total Sales}}{\text{Total Orders Placed}}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | Total Sales (from Q001) |
| :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 950 |
| ORD-102 | P-12 | 1 | 1200 |
| ORD-103 | P-11 | 4 | 1900 |
| ORD-104 | P-13 | 3 | 450 |
| ORD-105 | P-14 | 5 | 380 |
| ORD-106 | P-12 | 2 | 2250 |

- Total Sales = ₹7,130
- Total Distinct Orders = 6

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│   Average Order Value   │
│                         │
│        ₹ 1,188.33       │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Average Order Value = 
DIVIDE(
    [Total Sales], 
    [Total Orders Placed], 
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why not `AVERAGE(FactSales[SalesAmount])`?**
   - **Grammar Limitation:** `AVERAGE()` only accepts a single physical column, not a dynamic measure (`AVERAGE([Total Sales])` throws a syntax error).
   - **Grain Mismatch (Line-item vs Order):** Averaging rows in a transaction fact table calculates the average price per line item (item level), which misrepresents business performance if customers buy multiple distinct items in a single transaction.

2. **Measure Branching Reusability:**
   - Both `[Total Sales]` (Q001) and `[Total Orders Placed]` (Q004) are reused here, ensuring consistency across dashboard cards and reports.
