# Q001: Total Net Sales using Row-by-Row Iterator (SUMX)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 001 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
A retail enterprise stores transaction lines in `FactSales`. To optimize data warehouse storage, the engineering team does not maintain a pre-calculated `SalesAmount` column. The table only records `Quantity`, `UnitPrice`, and `DiscountAmount`.

The business needs an accurate DAX measure for **Total Net Sales** that evaluates dynamically across any report filter or visual slice.

$$\text{Total Net Sales} = \sum \Big( (\text{Quantity} \times \text{UnitPrice}) - \text{DiscountAmount} \Big)$$

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

| OrderID | Total Net Sales |
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
Total Net Sales = 
SUMX(
    FactSales,
    (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `SUMX` instead of `SUM`?**
   - `SUM()` accepts only a single physical column reference (`SUM(Table[Column])`). It cannot evaluate multi-column expressions.
   - `SUMX()` is an **iterator** that takes two arguments: the target table and an expression. It creates a **Row Context** over the table, evaluates the expression row-by-row, and aggregates the sum according to the active **Filter Context**.

2. **Measure vs. Calculated Column:**
   - A calculated column would permanently persist the calculated values into RAM and disk via the VertiPaq engine, inflating file size and refresh time.
   - A `SUMX` measure computes dynamically in-memory on query execution, keeping the model lightweight and scalable.

3. **DAX Syntax Best Practice:**
   - Always prefix columns with their table name (`FactSales[Quantity]`) to eliminate ambiguity with standalone measures.
