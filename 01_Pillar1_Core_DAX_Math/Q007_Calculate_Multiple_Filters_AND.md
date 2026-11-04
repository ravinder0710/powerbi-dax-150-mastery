# Q007: Multiple Filter Predicates in CALCULATE (Comma vs &&)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 007 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Regional leadership requires monitoring revenue for high-priority operational segments: products belonging to the **'Electronics'** category sold specifically in **'North'** region stores.

This requires applying multi-column, multi-table logical `AND` filtering across related dimension tables (`DimProduct` and `DimStore`) connected to `FactSales`.

---

## 📊 Sample Dataset

**Table 1: `DimProduct`**

| ProductID | ProductName | Category |
| :--- | :--- | :--- |
| P-11 | Wireless Mouse | Electronics |
| P-12 | LED Monitor | Electronics |
| P-13 | Coffee Mug | Home Decor |

**Table 2: `DimStore`**

| StoreID | StoreName | Region |
| :--- | :--- | :--- |
| S-1 | Delhi Central | North |
| S-2 | Mumbai South | West |
| S-3 | Chandigarh Hub | North |

**Table 3: `FactSales`**

| OrderID | ProductID | StoreID | Total Net Sales | Category | Region | Valid? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | S-1 | 950 | Electronics | North | ✅ Yes |
| ORD-102 | P-12 | S-2 | 1200 | Electronics | West | ❌ No |
| ORD-103 | P-11 | S-3 | 1900 | Electronics | North | ✅ Yes |
| ORD-104 | P-13 | S-1 | 450 | Home Decor | North | ❌ No |

Target Sales = $950 + 1900 = \mathbf{2,850}$

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│ North Electronics Sales │
│                         │
│         ₹ 2,850         │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
North Electronics Sales = 
CALCULATE(
    [Total Net Sales],
    DimProduct[Category] = "Electronics",
    DimStore[Region] = "North"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Comma `,` is Preferred over `&&`:**
   - Comma-separated filters are treated as independent arguments. The VertiPaq Storage Engine (SE) evaluates them in parallel using bitmap indexes and computes their intersection with maximum performance.
   - Using `&&` across multiple distinct tables can force the slower Formula Engine (FE) to materialize cross-table combinations in memory.
