# Q008: Logical OR and Table Constructor Filtering (IN Operator)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 008 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
The merchandising team needs to track combined net revenue for their lifestyle campaign categories: **'Electronics'** and **'Home Decor'**.

The solution must demonstrate filtering across multiple categorical values within a single column using the DAX `IN` operator.

---

## 📊 Sample Dataset

**Table 1: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

**Table 2: `FactSales`**

| OrderID | ProductID | Category | Total Net Sales | Included? |
| :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | Electronics | 950 | ✅ Yes |
| ORD-102 | P-12 | Electronics | 1200 | ✅ Yes |
| ORD-103 | P-11 | Electronics | 1900 | ✅ Yes |
| ORD-104 | P-13 | Home Decor | 450 | ✅ Yes |
| ORD-105 | P-14 | Office Supply | 380 | ❌ No |
| ORD-106 | P-12 | Electronics | 2250 | ✅ Yes |

Combined Net Sales = $950 + 1200 + 1900 + 450 + 2250 = \mathbf{6,750}$

---

## 🎯 Expected Output

```
┌───────────────────────────┐
│ Electronics & Home Decor  │
│                           │
│          ₹ 6,750          │
└───────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Net Sales],
    DimProduct[Category] IN { "Electronics", "Home Decor" }
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Curly Braces `{}` in DAX vs `()` in SQL:**
   - In SQL, parentheses are used for value tuples: `IN ('A', 'B')`.
   - In DAX, curly braces `{}` are the **Table Constructor** operator. It creates an anonymous single-column in-memory table. DAX evaluates `IN` as an optimized semi-join against this temporary table.
2. **`IN` vs `||` (Logical OR):**
   - Chained `||` requires sequential boolean evaluation across rows. `IN` translates into an optimized Storage Engine (SE) hash match structure.


---

## 🚨 Common Interview Traps & Defense

1. **Trap: Why not use Parentheses `()` like SQL?**
   - *Interviewer:* "Why did you write `Category IN { 'Electronics', 'Home Decor' }` instead of `('Electronics', 'Home Decor')`?"
   - *Defense:* In DAX, curly braces `{}` are the Table Constructor operator. They instantiate an anonymous single-column in-memory table. DAX evaluates `IN` as an optimized semi-join against this table.

2. **Trap: Performance vs Chained OR (`||`):**
   - *Interviewer:* "Why is `IN` faster than `Col = 'A' || Col = 'B'`?"
   - *Defense:* `IN` is mapped by the Storage Engine to a hash-match filter structure, avoiding multiple sequential boolean comparisons.
