# Q014: Average Selling Price (ASP) vs Average Order Value (AOV)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 014 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Retail merchandising analysts need to track product price realization through **Average Selling Price (ASP)** and compare it against basket checkout size (**AOV**).

Formulas:
- $\text{ASP} = \frac{\text{Total Net Sales}}{\text{Total Quantity Sold}}$ (Per-unit revenue realization)
- $\text{AOV} = \frac{\text{Total Net Sales}}{\text{Total Orders Placed}}$ (Per-order basket size)

The Golden Retail Equation:
$$\text{AOV} = \text{ASP} \times \text{UPT}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | Total Net Sales |
| :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | ₹ 950 |
| ORD-102 | P-12 | 1 | 1200 | ₹ 1,200 |
| ORD-103 | P-11 | 4 | 500 | ₹ 1,900 |
| ORD-104 | P-13 | 3 | 150 | ₹ 450 |
| ORD-105 | P-14 | 5 | 80 | ₹ 380 |
| ORD-106 | P-12 | 2 | 1200 | ₹ 2,250 |

Total Revenue = ₹ 7,130  
Total Quantity = 17  
Total Orders = 6  
- $\text{ASP} = 7130 / 17 = \mathbf{₹ 419.41}$ per unit
- $\text{AOV} = 7130 / 6 = \mathbf{₹ 1,188.33}$ per order

---

## 🎯 Expected Output

Visual: **Card Visuals**

```
┌─────────────────────────┐     ┌─────────────────────────┐
│  Avg Selling Price(ASP) │     │   Avg Order Value(AOV)  │
│                         │     │                         │
│        ₹ 419.41         │     │        ₹ 1,188.33       │
└─────────────────────────┘     └─────────────────────────┘
```

---

## 💡 Production DAX Solutions

### Measure 1: Average Selling Price (ASP)
```dax
Average Selling Price = 
DIVIDE(
    [Total Net Sales],
    SUM(FactSales[Quantity]),
    0
)
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

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `AVERAGE(FactSales[UnitPrice])` is Statistically Wrong:**
   - Averaging the `UnitPrice` column yields an **unweighted simple average**, completely ignoring sales volume.
   - Example: Selling 100 pens at ₹ 10 and 1 pen at ₹ 100 gives an unweighted average of ₹ 55, while true weighted ASP is ₹ 10.89!
   - True ASP must always divide aggregated revenue by total physical quantity.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Can ASP ever exceed AOV?**
   - *Interviewer:* "Is it possible for ASP to be greater than AOV in a normal retail business?"
   - *Defense:* "In standard retail, AOV is always greater than or equal to ASP because customers purchase 1 or more units per transaction ($UPT \ge 1$). Only with negative return adjustments or single fraction sales could an anomaly occur."
