# Q005: Average Order Value (AOV) & Basket Size (UPT)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 005 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive commercial leadership requires monitoring customer purchasing behavior using two key retail KPIs:
1. **Average Order Value (AOV):** The mean gross/net spend per distinct customer order.
2. **Units Per Transaction (UPT / Basket Size):** The mean number of product units purchased per customer order.

Formulas:
- $\text{AOV} = \frac{\text{Total Net Sales}}{\text{Total Orders Placed}}$
- $\text{UPT} = \frac{\text{Total Quantity Sold}}{\text{Total Orders Placed}}$

---

## 📊 Sample Dataset (`FactSales`)

| RowID | OrderID | Quantity | Total Net Sales (Q001) |
| :--- | :--- | :--- | :--- |
| 1 | **ORD-101** | 2 | 950 |
| 2 | **ORD-101** | 1 | (Same order second item) |
| 3 | **ORD-102** | 1 | 1200 |
| 4 | **ORD-103** | 4 | 1900 |
| 5 | **ORD-103** | 2 | (Same order second item) |
| 6 | **ORD-104** | 3 | 450 |
| 7 | **ORD-105** | 5 | 380 |

- Total Net Sales = ₹ 7,130
- Total Distinct Orders = 5
- Total Quantity Sold = 18

---

## 🎯 Expected Output

```
┌─────────────────────────┐     ┌─────────────────────────┐
│   Average Order Value   │     │  Units Per Transaction  │
│                         │     │                         │
│        ₹ 1,426.00       │     │          3.60           │
└─────────────────────────┘     └─────────────────────────┘
```

---

## 💡 Production DAX Solutions

### Measure 1: Total Quantity
```dax
Total Quantity = 
SUM(FactSales[Quantity])
```

### Measure 2: Average Order Value (AOV)
```dax
Average Order Value = 
DIVIDE(
    [Total Net Sales], 
    [Total Orders Placed], 
    0
)
```

### Measure 3: Units Per Transaction (UPT)
```dax
Units Per Transaction = 
DIVIDE(
    [Total Quantity], 
    [Total Orders Placed], 
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `AVERAGE()` Fails for AOV:**
   - **Syntax Restriction:** `AVERAGE()` only accepts a single column reference (`AVERAGE(Table[Column])`). Passing a dynamic measure (`AVERAGE([Total Net Sales])`) generates a compilation error.
   - **Granularity (Grain) Mismatch:** Averaging rows in a transaction fact table calculates the average price per *line item*, not per *order*. Orders containing multiple products would be severely understated.
2. **Safe Division:**
   - Both AOV and UPT must be wrapped in `DIVIDE()` to handle cases where zero orders are placed during an inactive period.
