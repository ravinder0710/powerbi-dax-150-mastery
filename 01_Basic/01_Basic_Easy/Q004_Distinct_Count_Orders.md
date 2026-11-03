# Q004: Distinct Order Counting (DISTINCTCOUNT vs COUNTROWS)

## 📌 Metadata
- **Module:** Module 1: Aggregations & Iterators
- **Tier:** 01. Basic Easy
- **Question Number:** 004 / 450
- **Target Role:** Data Analyst / Power BI Developer

---

## 🏢 Business Scenario
In an e-commerce transactional dataset (`FactSales`), orders often contain multiple line items (products). Consequently, the same `OrderID` repeats across multiple rows for each item purchased within that order.

The business needs an accurate KPI card displaying **Total Orders Placed**. Simply counting rows results in an inflated count of line items rather than unique transactions.

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
   - `DISTINCTCOUNT()` treats `BLANK()` as a distinct value and includes it in the count.
   - If business rules dictate that missing/null IDs must be ignored, use `DISTINCTCOUNTNOBLANK(FactSales[OrderID])`.

2. **`COUNTROWS(Table)` vs `COUNT(Column)`:**
   - Always prefer `COUNTROWS(FactSales)` over `COUNT(FactSales[OrderID])` when counting physical records.
   - `COUNTROWS` retrieves the row count directly from the VertiPaq storage engine metadata without reading column data pages, making it significantly faster on multi-million row tables.
