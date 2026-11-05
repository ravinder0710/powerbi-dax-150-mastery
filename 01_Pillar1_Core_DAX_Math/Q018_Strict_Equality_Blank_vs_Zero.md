# Q018: The Blank vs Zero Trap and Strict Equality (== vs =)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 018 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Commercial discount audit policies require differentiating between:
1. Products with an explicit, approved **₹ 0 discount** (standard full-price transactions).
2. Products with **missing discount data (`BLANK()`)** awaiting data entry.

In DAX, the standard equality operator (`=`) treats `0 = BLANK()` as `TRUE`. The solution must strictly isolate true zero discount items without counting blanks.

---

## 📊 Sample Dataset (`DimProduct`)

| ProductID | ProductName | DiscountGiven | Status | Strictly Zero Discount? |
| :--- | :--- | :--- | :--- | :--- |
| P-11 | Wireless Mouse | ₹ 50 | Has Discount | ❌ No |
| P-12 | LED Monitor | ₹ 0 | Approved Zero Discount | ✅ **YES** |
| P-13 | Coffee Mug | *(BLANK)* | Missing / Unapproved | ❌ **NO** |
| P-14 | Notebook | ₹ 0 | Approved Zero Discount | ✅ **YES** |

Strictly Zero Discount Products = 2 (P-12, P-14)

---

## 🎯 Expected Output

```
┌─────────────────────────┐
│ Zero Discount Products  │
│                         │
│            2            │
└─────────────────────────┘
```

---

## 💡 Production DAX Solution

```dax
Zero Discount Products = 
COUNTROWS(
    FILTER(
        DimProduct,
        DimProduct[DiscountGiven] == 0
    )
)
```

*Alternative Safe Pattern without `==`:*
```dax
Zero Discount Products = 
COUNTROWS(
    FILTER(
        DimProduct,
        NOT(ISBLANK(DimProduct[DiscountGiven])) && DimProduct[DiscountGiven] = 0
    )
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Lenient Equality (`=`) vs Strict Equality (`==`):**
   - Standard `=` performs type coercion: `0 = BLANK()` evaluates to `TRUE`.
   - Double `==` strictly enforces both data type identity and exact value equivalence: `0 == BLANK()` evaluates to `FALSE`.

---

## 🚨 Common Interview Traps & Defense

1. **Trap: Does `==` work on text strings?**
   - *Interviewer:* "Can `==` distinguish between empty string `""` and `BLANK()`?"
   - *Defense:* "Yes! In DAX, `"" = BLANK()` is TRUE, but `"" == BLANK()` is strictly FALSE."
