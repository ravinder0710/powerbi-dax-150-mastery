# Q003: Safe Division and Margin Calculation (DIVIDE vs /)

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 003 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
The business leadership requires a **Profit Margin %** KPI to monitor retail profitability across stores and orders. 

In real-world transactional databases, returns, exchanges, or canceled orders often have zero sales revenue ($\text{Total Sales} = 0$). Using the standard division operator (`/`) in DAX results in division-by-zero runtime errors, yielding `Infinity` or `NaN` and breaking visual rendering. The solution must ensure safe mathematical division.

Formula:
$$\text{Profit Margin \%} = \frac{\text{Total Profit}}{\text{Total Sales}}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | Quantity | UnitPrice | DiscountAmount | Total Sales | Total Profit |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | 2 | 500 | 50 | 950 | 350 |
| ORD-102 | 1 | 1200 | 0 | 1200 | 400 |
| ORD-103 | 4 | 500 | 100 | 1900 | 700 |
| ORD-104 | 3 | 150 | 0 | 450 | 180 |
| ORD-105 | 5 | 80 | 20 | 380 | 130 |
| ORD-106 | 2 | 1200 | 150 | 2250 | 650 |
| **ORD-107** | **0** | **0** | **0** | **0** | **0** |

*(Pre-requisite measures available: `[Total Sales]`, `[Total Profit]`)*

---

## 🎯 Expected Output

| OrderID | Total Sales | Total Profit | Profit Margin % |
| :--- | :--- | :--- | :--- |
| ORD-101 | 950 | 350 | 36.84% |
| ORD-102 | 1200 | 400 | 33.33% |
| ORD-103 | 1900 | 700 | 36.84% |
| ORD-104 | 450 | 180 | 40.00% |
| ORD-105 | 380 | 130 | 34.21% |
| ORD-106 | 2250 | 650 | 28.89% |
| **ORD-107** | **0** | **0** | **0.00% (or Blank)** |
| **Total** | **7130** | **2410** | **33.80%** |

---

## 💡 Production DAX Solution

```dax
Profit Margin % = 
DIVIDE(
    [Total Profit], 
    [Total Sales], 
    0
)
```

*Note: In enterprise dashboards where zero-value rows should be suppressed from visuals, the 3rd argument can be omitted to default to `BLANK()`:*
```dax
Profit Margin % = 
DIVIDE([Total Profit], [Total Sales])
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `DIVIDE()` instead of `/`?**
   - The `/` operator throws errors (`NaN` or `Infinity`) when the denominator is zero or empty.
   - `DIVIDE()` performs an internal check and handles null/zero denominators safely without requiring verbose `IF([Total Sales] = 0, ...)` logic.

2. **Default Behavior of 3rd Parameter:**
   - If the alternate result parameter is omitted, `DIVIDE()` returns `BLANK()`.
   - Returning `BLANK()` is often preferred in matrix and table visuals because Power BI's query engine automatically suppresses empty/blank rows, improving rendering speed and visual cleanliness.
