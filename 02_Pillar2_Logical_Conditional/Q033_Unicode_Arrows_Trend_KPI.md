# Q033: Dynamic Arrow Indicators & Variance KPI (`FORMAT` + Unicode)

## 📌 Metadata
- **Pillar:** Pillar 2: Logical, Text & Conditional DAX
- **Question Number:** 033 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive summary cards and matrix tables often require compact, self-explanatory KPI badges that combine directional arrows (`▲`, `▼`, `►`) and formatted percentage numbers into a single display string:
- **Strong Growth ($> +2\%$):** `"▲ +12.5%"`
- **Decline ($< -2\%$):** `"▼ -8.2%"`
- **Flat / Stable ($-2\% \text{ to } +2\%$):** `"► 0.5%"`
- **No Prior History (Blank):** `"-"`

An interviewer will ask:
> *"1. How do you convert numbers to formatted percentage text inside DAX?*
> *2. What operator is used to concatenate Unicode symbols with dynamic measure values?*
> *3. What is the key drawback of using `FORMAT()` on measures in terms of sorting and aggregation?"*

---

## 📦 Available Measures in Model
- `[Total Net Sales]`
- `[Sales Variance %]` (e.g., `0.125` for $+12.5\%$, `-0.082` for $-8.2\%$)

---

## 📊 Sample Dataset

| Category | Sales Variance % | Expected Output String | Trend Meaning |
| :--- | :---: | :---: | :--- |
| **Electronics** | `+0.125` ($+12.5\%$) | `"▲ +12.5%"` | Strong Growth |
| **Apparel** | `-0.082` ($-8.2\%$) | `"▼ -8.2%"` | Decline |
| **Home Essentials** | `+0.005` ($+0.5\%$) | `"► 0.5%"` | Flat / Stable |
| **New Category** | *(BLANK)* | `"-"` | No History |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Variance Trend KPI = 
VAR VarianceVal = [Sales Variance %]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(VarianceVal), "-",
    VarianceVal > 0.02,  "▲ +" & FORMAT(VarianceVal, "0.0%"),
    VarianceVal < -0.02, "▼ "  & FORMAT(VarianceVal, "0.0%"),
    "► "  & FORMAT(VarianceVal, "0.0%")
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `FORMAT()` Works:**
   - `FORMAT(<Value>, "<FormatString>")` converts a numeric scalar into a formatted text string (e.g. `0.125` $\rightarrow$ `"12.5%"`).
   - The ampersand `&` is DAX's string concatenation operator.

2. **The "Text Type" Interview Trap (Crucial):**
   - **Important:** Once a measure is wrapped in `FORMAT()`, its data type permanently becomes **Text / String**, not Numeric!
   - As a result, you cannot plot this measure on chart Y-axes, nor can you calculate averages or sums of it. It is strictly meant for **Card visual callouts, table labels, and tooltip displays**.
