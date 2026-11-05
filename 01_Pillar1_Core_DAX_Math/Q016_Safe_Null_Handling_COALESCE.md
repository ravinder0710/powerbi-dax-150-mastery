# Q016: Safe Null Handling & Fallback Display (COALESCE vs ISBLANK)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 016 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive dashboard tiles for new product releases or upcoming inventory lines show `(Blank)` because no sales transactions exist yet. 

Stakeholders request clean numerical reporting: explicitly rendering **₹ 0** instead of empty whitespace on card and table visuals.

---

## 📊 Sample Dataset (`DimProduct`)

| ProductID | ProductName | Status | Total Net Sales (Current) |
| :--- | :--- | :--- | :--- |
| P-11 | Wireless Mouse | Active | ₹ 950 |
| P-12 | LED Monitor | Active | ₹ 1,200 |
| P-20 | Gaming Laptop | New Launch | *(BLANK)* |
| P-13 | Coffee Mug | Active | ₹ 450 |
| P-21 | Smart Watch | Upcoming | *(BLANK)* |

---

## 🎯 Expected Output

| ProductName | Total Net Sales (Old) | Sales Display (New Measure) |
| :--- | :--- | :--- |
| Wireless Mouse | ₹ 950 | ₹ 950 |
| LED Monitor | ₹ 1,200 | ₹ 1,200 |
| **Gaming Laptop** | *(Blank)* | **₹ 0** |
| Coffee Mug | ₹ 450 | ₹ 450 |
| **Smart Watch** | *(Blank)* | **₹ 0** |

---

## 💡 Production DAX Solutions

### Solution 1: Modern COALESCE Function (Recommended)
```dax
Sales Display Value = 
COALESCE(
    [Total Net Sales],
    0
)
```

### Solution 2: Legacy IF + ISBLANK Pattern
```dax
Sales Display Value = 
IF(
    ISBLANK([Total Net Sales]),
    0,
    [Total Net Sales]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `COALESCE()` is Superior to `IF(ISBLANK())`:**
   - **Single Evaluation:** In `IF(ISBLANK([Sales]), 0, [Sales])`, the Formula Engine evaluates `[Sales]` twice (once for condition check, once for the false branch). `COALESCE()` evaluates the measure only once.
   - **Multi-Level Fallback:** `COALESCE(val1, val2, val3, 0)` simplifies complex tiered null-handling.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: The "+ 0" Trick Trap:**
   - *Interviewer:* "Can't we just write `[Total Net Sales] + 0` to convert blank to zero?"
   - *Defense:* "Writing `+ 0` destroys Power BI's automatic sparse matrix suppression. It forces table visuals to draw every single inactive product row in the database, degrading rendering performance."
