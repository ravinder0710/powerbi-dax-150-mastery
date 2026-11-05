# Q017: Counting Missing Records (COUNTBLANK vs COUNTROWS with FILTER)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 017 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Data Governance teams track completeness scores for CRM customer profiles. Compliance audits require identifying and quantifying how many customer records have missing demographic data (e.g. `City` is Blank/Null).

---

## 📊 Sample Dataset (`DimCustomer`)

| CustomerID | CustomerName | City | CustomerType |
| :--- | :--- | :--- | :--- |
| C-101 | Rahul Sharma | Delhi | Retail |
| C-102 | Priya Verma | *(BLANK)* | Wholesale |
| C-103 | Amit Patel | Mumbai | Retail |
| C-104 | Sneha Singh | *(BLANK)* | Retail |
| C-105 | Vikas Kumar | Jaipur | Retail |
| C-106 | Ananya Roy | *(BLANK)* | Wholesale |

Total Missing City Records = 3 (C-102, C-104, C-106)

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│ Incomplete Profiles     │
│                         │
│            3            │
└─────────────────────────┘
```

---

## 💡 Production DAX Solutions

### Solution 1: Direct Column Function (Fastest)
```dax
Incomplete Profiles = 
COUNTBLANK(DimCustomer[City])
```

### Solution 2: Table Filter Function (Multi-Column Capable)
```dax
Incomplete Profiles = 
COUNTROWS(
    FILTER(
        DimCustomer,
        ISBLANK(DimCustomer[City])
    )
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Limitations of `COUNTBLANK()`:**
   - `COUNTBLANK(Table[Column])` only operates on a single physical column.
   - For multi-column validation (e.g. `City` is blank OR `Phone` is blank), `COUNTROWS(FILTER(...))` is strictly required.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: `COUNT()` vs `COUNTBLANK()`:**
   - *Interviewer:* "If a column has 10 rows and 3 blanks, what do `COUNT` and `COUNTBLANK` return?"
   - *Defense:* "`COUNT(Column)` only counts non-blank cells (returning 7). `COUNTBLANK(Column)` only counts blank cells (returning 3)."
