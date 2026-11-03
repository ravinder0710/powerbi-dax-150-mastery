# Q002: Total Cost and Total Profit via Measure Branching

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 002 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
Following the creation of `[Total Sales]` (Q001), finance leadership needs visibility into `Total Cost` and `Total Profit`. 

Instead of writing redundant and complex standalone calculations, the organization mandates the use of **Measure Branching** (building higher-level business metrics on top of modular base measures) to maintain code scalability and performance.

Formulas:
- $$\text{Total Cost} = \sum (\text{Quantity} \times \text{UnitCost})$$
- $$\text{Total Profit} = \text{Total Sales} - \text{Total Cost}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount | UnitCost |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 | 300 |
| ORD-102 | P-12 | 1 | 1200 | 0 | 800 |
| ORD-103 | P-11 | 4 | 500 | 100 | 300 |
| ORD-104 | P-13 | 3 | 150 | 0 | 90 |
| ORD-105 | P-14 | 5 | 80 | 20 | 50 |
| ORD-106 | P-12 | 2 | 1200 | 150 | 800 |

---

## 🎯 Expected Output

| OrderID | Total Sales | Total Cost | Total Profit |
| :--- | :--- | :--- | :--- |
| ORD-101 | 950 | 600 | 350 |
| ORD-102 | 1200 | 800 | 400 |
| ORD-103 | 1900 | 1200 | 700 |
| ORD-104 | 450 | 270 | 180 |
| ORD-105 | 380 | 250 | 130 |
| ORD-106 | 2250 | 1600 | 650 |
| **Total** | **7130** | **4720** | **2410** |

---

## 💡 Production DAX Solution

### Step 1: Base Measure for Total Cost
```dax
Total Cost = 
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitCost]
)
```

### Step 2: Branched Measure for Total Profit
```dax
Total Profit = [Total Sales] - [Total Cost]
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Common Syntax Pitfall:**
   - Both columns (`Quantity` and `UnitCost`) must reside strictly inside the second parameter of `SUMX`. Placing the closing parenthesis right after `UnitCost` terminates the row context and throws a context error when referencing `Quantity`.

2. **Why Measure Branching Matters:**
   - **Maintainability:** Modifying upstream business logic (e.g. adjusting tax in `[Total Sales]`) automatically cascades down to `[Total Profit]` without manual refactoring across dozens of formulas.
   - **DAX Formula Engine Caching:** VertiPaq can cache intermediate calculation results of base measures within the same visual execution step, avoiding re-scanning the physical fact table.
   - **Troubleshooting:** Isolating bugs in reporting becomes straightforward because individual components (`Sales`, `Cost`) can be tested independently.
