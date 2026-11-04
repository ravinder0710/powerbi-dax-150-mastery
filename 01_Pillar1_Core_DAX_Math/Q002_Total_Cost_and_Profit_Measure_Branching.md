# Q002: Total Cost & Profit using Measure Branching

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 002 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
The financial leadership needs to track **Cost of Goods Sold (COGS)** and net **Gross Profit** to understand bottom-line profitability after product discounts.

Instead of duplicating the revenue and cost arithmetic inside a single large formula, enterprise engineering standards require **Measure Branching**—reusing the foundational measure `[Total Net Sales]` (from Q001) to compute `[Total Profit]`.

Formulas:
- $\text{Total Cost} = \sum (\text{Quantity} \times \text{UnitCost})$
- $\text{Total Profit} = \text{Total Net Sales} - \text{Total Cost}$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount | UnitCost | Total Net Sales (Q001) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 | 300 | 950 |
| ORD-102 | P-12 | 1 | 1200 | 0 | 800 | 1200 |
| ORD-103 | P-11 | 4 | 500 | 100 | 300 | 1900 |
| ORD-104 | P-13 | 3 | 150 | 0 | 90 | 450 |
| ORD-105 | P-14 | 5 | 80 | 20 | 50 | 380 |
| ORD-106 | P-12 | 2 | 1200 | 150 | 800 | 2250 |

---

## 🎯 Expected Output

| OrderID | Total Net Sales | Total Cost | Total Profit |
| :--- | :--- | :--- | :--- |
| ORD-101 | 950 | 600 | 350 |
| ORD-102 | 1200 | 800 | 400 |
| ORD-103 | 1900 | 1200 | 700 |
| ORD-104 | 450 | 270 | 180 |
| ORD-105 | 380 | 250 | 130 |
| ORD-106 | 2250 | 1600 | 650 |
| **Total** | **7130** | **4720** | **2410** |

---

## 💡 Production DAX Solutions

### Measure 1: Total Cost (COGS)
```dax
Total Cost = 
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitCost]
)
```

### Measure 2: Total Profit (Measure Branching)
```dax
Total Profit = 
[Total Net Sales] - [Total Cost]
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Measure Branching?**
   - **DRY Principle:** Keeps code modular. Any future adjustment to discount logic in `[Total Net Sales]` propagates automatically to `[Total Profit]`.
   - **Formula Engine Cache:** VertiPaq can cache intermediate results of base measures across multiple visuals on a page.
   - **Readability & Debugging:** Isolates errors quickly during unit testing compared to complex nested formulas.

2. **Syntax Discipline:**
   - Table columns MUST have table prefixes: `FactSales[UnitCost]`.
   - Dynamic measures MUST be standalone: `[Total Net Sales]`.
