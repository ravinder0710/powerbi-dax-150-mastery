# Q031: KPI Target Status Badge (`SWITCH(TRUE())` with Measure Branching)

## 📌 Metadata
- **Pillar:** Pillar 2: Logical, Text & Conditional DAX
- **Question Number:** 031 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive scorecards require dynamic visual status badges (emojis / status chips) to help senior leadership spot product profitability at a glance:
- **High Margin ($\ge 20\%$):** `"🟢 High Margin"`
- **Moderate Margin ($10\% \text{ to } 20\%$):** `"🟡 Moderate Margin"`
- **Low Margin ($< 10\%$):** `"🔴 Low Margin"`
- **Missing / Zero Sales:** `"⚪ No Data"`

An interviewer will ask:
> *"1. Why is `SWITCH(TRUE(), ...)` preferred over deeply nested `IF()` statements in production DAX?*
> *2. What happens if you compare against `20` instead of `0.20` when evaluating percentage measures?*
> *3. Why must `ISBLANK()` be evaluated first before threshold checks?"*

---

## 📦 Available Measures in Model
- `[Total Net Sales]`
- `[Total Cost]`
- `[Total Profit]`
- `[Profit Margin %] = DIVIDE([Total Profit], [Total Net Sales], 0)`

---

## 📊 Sample Dataset

| Product | Total Net Sales | Total Profit | Profit Margin % | Expected Status Badge |
| :--- | :---: | :---: | :---: | :--- |
| **Laptops** | ₹ 1,00,000 | ₹ 25,000 | **25.0%** (`0.25`) | `"🟢 High Margin"` |
| **Monitors** | ₹ 50,000 | ₹ 7,500 | **15.0%** (`0.15`) | `"🟡 Moderate Margin"` |
| **Accessories** | ₹ 20,000 | ₹ 1,000 | **5.0%** (`0.05`) | `"🔴 Low Margin"` |
| **New Launch** | ₹ 0 | ₹ 0 | *(BLANK)* | `"⚪ No Data"` |

---

## 🎯 Expected Output

| Metric / Slicer Row | `[Profit Margin %]` Value | Output Badge |
| :--- | :---: | :--- |
| Laptops | `0.25` | `"🟢 High Margin"` |
| Monitors | `0.15` | `"🟡 Moderate Margin"` |
| Accessories | `0.05` | `"🔴 Low Margin"` |
| New Launch | `BLANK()` | `"⚪ No Data"` |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Margin Health Status = 
SWITCH(
    TRUE(),
    ISBLANK([Profit Margin %]), "⚪ No Data",
    [Profit Margin %] >= 0.20, "🟢 High Margin",
    [Profit Margin %] >= 0.10, "🟡 Moderate Margin",
    "🔴 Low Margin"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Percentage Representation in DAX Engine (The 20 vs 0.20 Trap):**
   - In Power BI, percentage formatting is purely a visual cosmetic display.
   - Internally, the VertiPaq engine stores 20% as the decimal scalar `0.20`.
   - Checking `[Profit Margin %] >= 20` checks for $2000\%$ profit, causing all normal business margins to fail and evaluate to `"🔴 Low Margin"`.

2. **`ISBLANK()` Precedence:**
   - In DAX, `BLANK()` is leniently treated as `0` in numerical comparisons.
   - If `ISBLANK()` is not checked first, a blank margin evaluates to $0 < 0.10$, erroneously displaying `"🔴 Low Margin"` instead of `"⚪ No Data"`.

3. **Evaluation Ladder Principle:**
   - `SWITCH(TRUE(), ...)` stops at the **first condition that evaluates to TRUE**.
   - Always order numeric thresholds from highest to lowest (or handle specific edge cases first) to avoid premature condition matches.
