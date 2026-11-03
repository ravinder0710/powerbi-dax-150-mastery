# Q008: Logical OR and Table Constructor Filtering (IN vs ||)

## 📌 Metadata
- **Module:** Module 4: Filter Modification & Context
- **Tier:** 01. Basic Easy
- **Question Number:** 008 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
The merchandising team wants to track combined revenue for their primary lifestyle product lines: **'Electronics'** and **'Home Decor'**.

The solution must demonstrate filtering across multiple categorical values within a single column, comparing the traditional logical OR operator (`||`) with the DAX `IN` operator.

---

## 📊 Sample Dataset

**Table 1: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |
| P-14 | Notebook | Office Supply |

**Table 2: `FactSales`** (Pre-computed `[Total Sales]` available)

| OrderID | ProductID | Total Sales (from Q001) | Category | Included? |
| :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 950 | Electronics | ✅ Yes |
| ORD-102 | P-12 | 1200 | Electronics | ✅ Yes |
| ORD-103 | P-11 | 1900 | Electronics | ✅ Yes |
| ORD-104 | P-13 | 450 | Home Decor | ✅ Yes |
| ORD-105 | P-14 | 380 | Office Supply | ❌ No |
| ORD-106 | P-12 | 2250 | Electronics | ✅ Yes |

Combined Sales = $950 + 1200 + 1900 + 450 + 2250 = \mathbf{6,750}$

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│ Electronics & Home Decor│
│                         │
│         ₹ 6,750         │
└─────────────────────────┘
```

---

## 💡 Production DAX Solutions

### Recommended Approach (Using `IN` Operator):
```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Sales],
    DimProduct[Category] IN { "Electronics", "Home Decor" }
)
```

### Alternative Approach (Using `||` Logical OR):
```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Sales],
    DimProduct[Category] = "Electronics" || DimProduct[Category] = "Home Decor"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **SQL vs DAX `IN` Syntax Trap:**
   - In SQL, parentheses are used: `WHERE Category IN ('A', 'B')`.
   - In DAX, **curly braces `{}`** are strictly required: `Category IN { "A", "B" }`.
   - **Why?** The curly braces `{}` represent the **Table Constructor** in DAX. It instantiates an anonymous single-column, in-memory table.

2. **Why `IN` is Preferred over `||` (OR):**
   - **Readability & Scalability:** Chaining multiple OR conditions (`Col = "A" || Col = "B" || Col = "C"`) is error-prone and tedious.
   - **Engine Performance:** The VertiPaq Storage Engine converts `IN { ... }` into an optimized hash join filter structure rather than executing multiple individual boolean disjunction evaluations.
