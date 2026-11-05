# Q012: Row-by-Row Minimum with Zero Exclusion (MINX & FILTER)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 012 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Retail stores occasionally distribute promotional samples, gift-with-purchase items, or replacement components recorded with `UnitPrice = 0`. 

Pricing analysts need to determine the **lowest paid transaction value** across line items, strictly excluding ₹ 0 freebies to avoid distorting baseline price floor analytics.

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | (Line Value: Qty × Price) | Note |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | ₹ 1,000 | Paid Line |
| ORD-102 | P-12 | 1 | 1200 | ₹ 1,200 | Paid Line |
| ORD-103 | P-15 | 1 | 0 | ₹ 0 | Freebie Sample (Exclude) |
| ORD-104 | P-13 | 3 | 150 | **₹ 450** | **Lowest Paid Line!** |
| ORD-105 | P-14 | 5 | 100 | ₹ 500 | Paid Line |

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│  Min Paid Sale Value    │
│                         │
│          ₹ 450          │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Min Paid Sale Value = 
MINX(
    FILTER(
        FactSales,
        (FactSales[Quantity] * FactSales[UnitPrice]) > 0
    ),
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

*Optimized Variation (Checking UnitPrice > 0):*
```dax
Min Paid Sale Value = 
MINX(
    FILTER(
        FactSales,
        FactSales[UnitPrice] > 0
    ),
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `FILTER()` Operates inside `MINX()`:**
   - `FILTER(Table, Condition)` is a table iterator function that returns a virtual filtered sub-table.
   - Rows where line revenue equals zero are filtered out before `MINX()` evaluates the minimum scalar value.

2. **Handling All-Zero Edge Cases:**
   - If all rows in the active filter context have zero value, `MINX` safely returns `BLANK()` without throwing runtime exceptions.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Why not `CALCULATE(MINX(...), UnitPrice > 0)`?**
   - *Interviewer:* "Can we use `CALCULATE` instead of `FILTER` inside `MINX`?"
   - *Defense:* "Yes! `CALCULATE(MINX(FactSales, Qty * Price), FactSales[UnitPrice] > 0)` is highly performant because `CALCULATE` leverages VertiPaq Storage Engine bitmap filtering directly."

2. **Trap: Plain `MIN()` pitfall:**
   - *Interviewer:* "What happens if you run simple `MIN(FactSales[UnitPrice])`?"
   - *Defense:* "It returns ₹ 0 due to freebie rows, violating the commercial requirement to identify the lowest paid transaction."
