# Q004: Distinct Order Counting (DISTINCTCOUNT vs COUNTROWS)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 004 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In an e-commerce transactional dataset (`FactSales`), each row represents an individual line item. An order placed by a customer can have multiple line items, resulting in repeating `OrderID` values.

Business leaders need a high-level KPI card displaying **Total Orders Placed**. Counting table records directly inflates the transaction count, confusing total items sold with distinct customer checkout orders.

---

## 📊 Sample Dataset (`FactSales`)

| RowID | OrderID | ProductID | Quantity | UnitPrice |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **ORD-101** | P-11 | 2 | 500 |
| 2 | **ORD-101** | P-14 | 1 | 80 |
| 3 | **ORD-102** | P-12 | 1 | 1200 |
| 4 | **ORD-103** | P-11 | 4 | 500 |
| 5 | **ORD-103** | P-13 | 2 | 150 |
| 6 | **ORD-104** | P-13 | 3 | 150 |
| 7 | **ORD-105** | P-14 | 5 | 80 |

Total Rows = 7  
Distinct Orders = 5 (`ORD-101`, `ORD-102`, `ORD-103`, `ORD-104`, `ORD-105`)

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│   Total Orders Placed   │
│                         │
│            5            │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Total Orders Placed = 
DISTINCTCOUNT(FactSales[OrderID])
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **`DISTINCTCOUNT` vs `DISTINCTCOUNTNOBLANK`:**
   - `DISTINCTCOUNT()` counts `BLANK()` as a distinct valid value.
   - If business rules dictate that missing/null transaction IDs must be ignored, use `DISTINCTCOUNTNOBLANK(FactSales[OrderID])`.
2. **`COUNTROWS(Table)` vs `COUNT(Column)` Performance:**
   - Always prefer `COUNTROWS(FactSales)` over `COUNT(FactSales[OrderID])` when counting physical records.
   - `COUNTROWS` retrieves the row count directly from VertiPaq engine table metadata without scanning column data pages.
