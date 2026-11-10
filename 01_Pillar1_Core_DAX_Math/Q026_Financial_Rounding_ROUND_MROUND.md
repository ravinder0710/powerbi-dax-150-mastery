# Q026: Financial Rounding & Business Multiples (`ROUND` vs `MROUND`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 026 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Enterprise financial and logistical systems require distinct rounding semantics across reporting layers:
1. **Financial Precision:** Currency margins, GST, and tax liabilities require rounding to **2 fixed decimal places** to reconcile with General Ledger systems.
2. **Operations & Cash Settlements:** Delivery fleet partner incentives and physical cash transactions must be rounded to the **nearest business multiple** (e.g., nearest ₹ 5 or ₹ 10 bill).

An interviewer will ask:
> *"1. What is the fundamental difference between `ROUND` and `MROUND` in DAX?*
> *2. What happens if you pass a negative number of digits to `ROUND`?*
> *3. What runtime error occurs in `MROUND` if the sign of the number and the multiple do not match?"*

---

## 📊 Sample Dataset (`FactDeliveryPayout`)

| DeliveryID | PartnerID | CalculatedIncentive |
| :---: | :---: | :---: |
| `1` | P101 | ₹ 48.333 |
| `2` | P102 | ₹ 42.148 |
| `3` | P103 | ₹ 27.500 |

---

## 🎯 Expected Output

| Metric / Measure | Input Value | Expected Result | Technical Rule |
| :--- | :---: | :---: | :--- |
| `Incentive Rounded (2 Decimals)` | ₹ 48.333 | **₹ 48.33** | 2 decimal places precision |
| `Cash Payout (Multiple of 5)` | ₹ 48.333 | **₹ 50.00** | Nearest multiple of 5 |
| `Cash Payout (Multiple of 5)` | ₹ 42.148 | **₹ 40.00** | Nearest multiple of 5 |

---

## 💡 Production DAX Solutions

### Measure 1: Standard Decimal Rounding (`ROUND`):
```dax
Incentive Rounded (2 Decimals) = 
ROUND(
    SUM(FactDeliveryPayout[CalculatedIncentive]),
    2
)
```

### Measure 2: Business Multiple Rounding (`MROUND`):
```dax
Cash Payout (Multiple of 5) = 
MROUND(
    SUM(FactDeliveryPayout[CalculatedIncentive]),
    5
)
```

### Directional Multiple Rounding (`CEILING` vs `FLOOR`):
```dax
-- Always round UP to nearest multiple of 5
Cash Payout Ceiling = CEILING(SUM(FactDeliveryPayout[CalculatedIncentive]), 5)

-- Always round DOWN to nearest multiple of 5
Cash Payout Floor = FLOOR(SUM(FactDeliveryPayout[CalculatedIncentive]), 5)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **`ROUND` Negative Digits Trap (Favorite Interview Question):**
   - If `num_digits > 0`: Rounds to decimal places (`ROUND(1234.56, 1)` $\rightarrow$ `1234.6`).
   - If `num_digits = 0`: Rounds to nearest integer (`ROUND(1234.56, 0)` $\rightarrow$ `1235`).
   - If `num_digits < 0`: Rounds to powers of 10 (`ROUND(1234.56, -2)` $\rightarrow$ `1200`)!

2. **`MROUND` Sign Mismatch Error Trap:**
   - In DAX, `MROUND(number, multiple)` requires both arguments to have the **same sign**.
   - If `number` is negative (`-48`) and `multiple` is positive (`5`), DAX throws a calculation error: *"Both arguments must have the same sign."*
   - Senior Defense: `IF([Incentive] < 0, -MROUND(ABS([Incentive]), 5), MROUND([Incentive], 5))`
