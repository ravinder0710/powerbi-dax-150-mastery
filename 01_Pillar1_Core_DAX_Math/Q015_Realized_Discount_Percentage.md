# Q015: Realized Discount Percentage (Discount vs Gross Sales)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 015 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Finance and commercial audit teams require monitoring discount erosion: calculating the exact percentage of gross retail value that was discounted during promotional campaigns.

$$\text{Realized Discount \%} = \frac{\text{Total Discount Amount}}{\text{Total Gross Sales}}$$

$$\text{Total Net Sales} = \text{Total Gross Sales} - \text{Total Discount Amount}$$

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | ProductID | Quantity | UnitPrice | DiscountAmount | (Gross: Qty × Price) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ORD-101 | P-11 | 2 | 500 | 50 | ₹ 1,000 |
| ORD-102 | P-12 | 1 | 1200 | 0 | ₹ 1,200 |
| ORD-103 | P-11 | 4 | 500 | 100 | ₹ 2,000 |
| ORD-104 | P-13 | 3 | 150 | 0 | ₹ 450 |
| ORD-105 | P-14 | 5 | 80 | 20 | ₹ 400 |
| ORD-106 | P-12 | 2 | 1200 | 150 | ₹ 2,400 |

Total Gross Sales = ₹ 7,450  
Total Discount Amount = ₹ 320  
Realized Discount % = $320 / 7450 = \mathbf{4.30\%}$

---

## 🎯 Expected Output

```
┌─────────────────────────┐     ┌─────────────────────────┐
│    Total Gross Sales    │     │   Realized Discount %   │
│                         │     │                         │
│         ₹ 7,450         │     │          4.30%          │
└─────────────────────────┘     └─────────────────────────┘
```

---

## 💡 Production DAX Solutions

### Measure 1: Total Gross Sales
```dax
Total Gross Sales = 
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

### Measure 2: Total Discount Amount
```dax
Total Discount Amount = 
SUM(FactSales[DiscountAmount])
```

### Measure 3: Realized Discount % (Measure Branching)
```dax
Realized Discount % = 
DIVIDE(
    [Total Discount Amount],
    [Total Gross Sales],
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Gross Sales vs Net Sales Financial Difference:**
   - **Gross Sales:** Revenue calculated at the original sticker list price before deductions ($\text{Qty} \times \text{UnitPrice}$).
   - **Net Sales:** Actual recognized cash flow collected from customers after deducting discounts and returns ($\text{Gross Sales} - \text{Discount}$). Company net profit is strictly derived from Net Sales.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Why not divide by Net Sales?**
   - *Interviewer:* "Why is the denominator Gross Sales instead of Net Sales?"
   - *Defense:* "Commercial discounts are granted on the original retail sticker price (Gross MRP). Dividing by Net Sales would artificially inflate the discount rate."

2. **Trap: Inverted Numerator / Denominator:**
   - *Interviewer:* "What happens if someone writes `DIVIDE([Gross Sales], [Discount Amount])`?"
   - *Defense:* "It calculates the inverse ratio (producing over 2300%), which corrupts dashboard reporting."
