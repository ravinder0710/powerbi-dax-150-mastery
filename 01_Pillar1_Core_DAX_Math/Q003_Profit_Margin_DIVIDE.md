# Q003: Safe Division and Margin Calculation (DIVIDE vs /)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 003 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive management requires monitoring bottom-line profitability through **Profit Margin %**. 

In real-world retail transactions, returns, canceled orders, or zero-sale promotional lines lead to $\text{Total Net Sales} = 0$. Using the standard division operator (`/`) in DAX throws runtime division-by-zero errors (`Infinity` or `NaN`), breaking report visuals. The calculation must guarantee production-safe division.

Formula:
$$\text{Profit Margin \%} = \frac{\text{Total Profit}}{\text{Total Net Sales}}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | Total Net Sales (Q001) | Total Profit (Q002) |
| :--- | :--- | :--- |
| ORD-101 | 950 | 350 |
| ORD-102 | 1200 | 400 |
| ORD-103 | 1900 | 700 |
| ORD-104 | 450 | 180 |
| ORD-105 | 380 | 130 |
| ORD-106 | 2250 | 650 |
| **ORD-107** | **0** | **0** |

---

## 🎯 Expected Output

| OrderID | Total Net Sales | Total Profit | Profit Margin % |
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
    [Total Net Sales], 
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `DIVIDE` over `/`?**
   - The `/` operator results in `Infinity` or `NaN` errors when the denominator is zero. `DIVIDE()` catches this internally and returns a safe fallback.
2. **Default Alternate Result:**
   - If the 3rd parameter is omitted, `DIVIDE()` defaults to `BLANK()`.
3. **Engine Optimization (Blank vs Zero):**
   - Returning `BLANK()` enables Power BI visuals to automatically suppress empty rows, significantly speeding up visual rendering compared to forcing `0` across millions of inactive records.
