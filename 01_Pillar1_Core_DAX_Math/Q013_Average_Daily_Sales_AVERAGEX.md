# Q013: Average Daily Sales using AVERAGEX & VALUES

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 013 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive management needs an operational benchmark metric: **Average Daily Sales (ADS)**. 

Comparing monthly aggregate revenue is inherently misleading because different calendar months have different day counts (e.g. February has 28 days vs March with 31 days). ADS normalizes time comparisons for run-rate projections.

$$\text{Average Daily Sales} = \frac{\text{Total Net Sales}}{\text{Number of Active Days}}$$

---

## 📊 Sample Dataset (`DimDate` & `FactSales`)

| DateKey | Date | DayName | Daily Sales Evaluated |
| :--- | :--- | :--- | :--- |
| 20260101 | 01-Jan-2026 | Thursday | ₹ 2,150 |
| 20260102 | 02-Jan-2026 | Friday | ₹ 1,200 |
| 20260103 | 03-Jan-2026 | Saturday | ₹ 1,900 |
| 20260104 | 04-Jan-2026 | Sunday | ₹ 450 |
| 20260105 | 05-Jan-2026 | Monday | ₹ 1,430 |

Total Revenue = ₹ 7,130 across 5 distinct active days.  
Average Daily Sales = $7130 / 5 = \mathbf{₹ 1,426.00}$

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│   Average Daily Sales   │
│                         │
│        ₹ 1,426.00       │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Average Daily Sales = 
AVERAGEX(
    VALUES(DimDate[Date]),
    [Total Net Sales]
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `VALUES()` Interacts with `AVERAGEX()`:**
   - `VALUES(DimDate[Date])` returns a 1-column table of distinct dates visible under the current active filter context.
   - `AVERAGEX` iterates through each individual date, evaluates `[Total Net Sales]` for that day, and computes the arithmetic mean.

2. **The Magic of Context Transition:**
   - As `AVERAGEX` iterates over `VALUES(DimDate[Date])`, a Row Context exists for each date.
   - Because `[Total Net Sales]` is a calculated measure, its implicit `CALCULATE()` converts that row context into an equivalent **Filter Context**, automatically filtering the sales to that exact single calendar date!

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Why not `AVERAGEX(VALUES(DimDate[Date]), SUM(FactSales[Quantity]) * SUM(FactSales[UnitPrice]))`?**
   - *Interviewer:* "What happens if we write raw sum expressions instead of referencing a measure `[Total Net Sales]`?"
   - *Defense:* "Without referencing a measure or wrapping in `CALCULATE()`, Context Transition does NOT happen! The engine would compute the grand total sales across all days for each date row and average that same grand total, giving a completely incorrect answer."

2. **Trap: `VALUES()` vs `ALL()` in AVERAGEX:**
   - *Interviewer:* "Can we write `ALL(DimDate[Date])` instead of `VALUES(DimDate[Date])`?"
   - *Defense:* "No! `ALL()` ignores report slicers and would average across all 365 calendar days. `VALUES()` respects slicers, correctly averaging only the days selected by the user."
