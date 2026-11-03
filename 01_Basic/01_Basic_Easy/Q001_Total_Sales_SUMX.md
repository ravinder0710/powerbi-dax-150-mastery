# Q001: Total Sales Revenue using Row-by-Row Iterator (SUMX)

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 001 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
A retail enterprise does not store pre-computed `SalesAmount` in its transactional table (`FactSales`) to save database storage. The table only stores `Quantity`, `UnitPrice`, and `DiscountAmount`. The reporting team needs a dynamic measure to calculate Total Net Sales Revenue across any slice or filter in the report.

Formula:
$$\text{Total Sales} = (\text{Quantity} \times \text{UnitPrice}) - \text{DiscountAmount}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount |
| :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 |
| ORD-102 | P-12 | 1 | 1200 | 0 |
| ORD-103 | P-11 | 4 | 500 | 100 |
| ORD-104 | P-13 | 3 | 150 | 0 |
| ORD-105 | P-14 | 5 | 80 | 20 |
| ORD-106 | P-12 | 2 | 1200 | 150 |

---

## 🎯 Expected Output

| OrderID | Total Sales |
| :--- | :--- |
| ORD-101 | 950 |
| ORD-102 | 1200 |
| ORD-103 | 1900 |
| ORD-104 | 450 |
| ORD-105 | 380 |
| ORD-106 | 2250 |
| **Total** | **7130** |

---

## 💡 Production DAX Solution

```dax
Total Sales = 
SUMX(
    FactSales,
    (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `SUMX` instead of `SUM`?**
   - `SUM` is an aggregation function that takes only a single column reference (`SUM(Table[Column])`). It cannot evaluate an expression across multiple columns.
   - `SUMX` is an **iterator function** that takes two arguments: the target table and an expression. It initiates a **Row Context** over `FactSales`, computes `(Quantity * UnitPrice) - DiscountAmount` row-by-row, and then aggregates the results using the active filter context.

2. **Calculated Column vs. Measure:**
   - A calculated column would permanently store the calculated amount in RAM/disk within the VertiPaq database, increasing file size and refresh time.
   - Using a `SUMX` measure keeps the data model lean and performs computation on-demand.

3. **DAX Best Practice:**
   - Always prefix column names with the table name (`FactSales[Quantity]`) to distinguish them from standalone measures (`[Total Sales]`).
