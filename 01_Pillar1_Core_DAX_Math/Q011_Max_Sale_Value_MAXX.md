# Q011: Row-by-Row Maximum Value using MAXX Iterator

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 011 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Retail store leadership and fraud detection teams need to monitor outlier transaction records: identifying the **highest single-item sales value** recorded in a line item. 

The transaction fact table (`FactSales`) stores `Quantity` and `UnitPrice`, but does not persist an extended line-item amount column. The DAX solution must evaluate line-item revenue dynamically across rows and extract the maximum.

$$\text{Max Single Line Sale} = \max_{\text{rows}} (\text{Quantity} \times \text{UnitPrice})$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | (Line Value: Qty × Price) |
| :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | ₹ 1,000 |
| ORD-102 | P-12 | 1 | 1200 | ₹ 1,200 |
| ORD-103 | P-11 | 4 | 500 | **₹ 2,000** (Highest Single Line!) |
| ORD-104 | P-13 | 3 | 150 | ₹ 450 |
| ORD-105 | P-14 | 5 | 80 | ₹ 400 |

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│   Max Sale Value        │
│                         │
│         ₹ 2,000         │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Max Sale Value = 
MAXX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `MAX()` Fails on Multi-Column Expressions:**
   - `MAX(Table[Column])` accepts only a single physical column reference. It cannot evaluate mathematical arithmetic (`Qty * Price`) across multiple columns.
   - `MAXX(Table, Expression)` is an iterator function that initiates a **Row Context** over `FactSales`, evaluates `Quantity * UnitPrice` row-by-row, and extracts the maximum resulting scalar value.

2. **Storage Engine Metadata Optimization:**
   - When extracting the maximum of a single column directly (e.g. `MAX(FactSales[UnitPrice])`), use `MAX()` rather than `MAXX()`. The VertiPaq storage engine maintains min/max values in column dictionary metadata, returning scalar results without iterating table pages.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Does `MAX()` ever accept two arguments?**
   - *Interviewer:* "Can `MAX()` accept two parameters in DAX?"
   - *Defense:* "Yes! `MAX(scalar1, scalar2)` compares two scalar values (e.g. `MAX(10, 25)` returns 25). However, for multi-row table expressions, iterator `MAXX()` is required."

2. **Trap: Calculated Column for Max Sale:**
   - *Interviewer:* "Why not create a calculated column `LineTotal = Qty * Price` and use `MAX(FactSales[LineTotal])`?"
   - *Defense:* "Calculated columns consume uncompressed memory in VertiPaq. For high-volume transaction fact tables, a dynamic `MAXX()` measure preserves memory and model agility."
