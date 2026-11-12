# Q032: Dynamic Conditional Formatting via Hex Codes

## 📌 Metadata
- **Pillar:** Pillar 2: Logical, Text & Conditional DAX
- **Question Number:** 032 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive Power BI tables, matrix visuals, and KPI cards require automated dynamic color formatting (Field Value Conditional Formatting) to visually highlight financial performance without relying on manual visual color picker rules:
- **High Margin ($\ge 20\%$):** Soft Emerald Green (`"#2ECC71"`)
- **Moderate Margin ($10\% \text{ to } 20\%$):** Warm Amber Yellow (`"#F1C40F"`)
- **Low / Negative Margin ($< 10\%$):** Coral Red (`"#E74C3C"`)
- **Missing / Blank Data:** Neutral Gray (`"#BDC3C7"`)

An interviewer will ask:
> *"1. How do you apply DAX-driven dynamic colors in Power BI visuals using 'Format by Field Value'?*
> *2. Why should the red condition be the default fallback rather than strictly checking `>= 0`?*
> *3. Can this measure be used across Background Color, Font Color, and Data Bar colors?"*

---

## 📦 Available Measures in Model
- `[Total Net Sales]`
- `[Total Profit]`
- `[Profit Margin %]`

---

## 📊 Sample Dataset

| Product | Profit Margin % | Expected Color Hex Code | Target Visual Display |
| :--- | :---: | :---: | :---: |
| **Laptops** | **25.0%** (`0.25`) | `"#2ECC71"` | 🟢 Green Background |
| **Monitors** | **15.0%** (`0.15`) | `"#F1C40F"` | 🟡 Yellow Background |
| **Accessories** | **5.0%** (`0.05`) | `"#E74C3C"` | 🔴 Red Background |
| **Clearance (Loss)** | **-8.0%** (`-0.08`) | `"#E74C3C"` | 🔴 Red Background |
| **New Launch** | *(BLANK)* | `"#BDC3C7"` | ⚪ Gray Background |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Margin Color Code = 
SWITCH(
    TRUE(),
    ISBLANK([Profit Margin %]), "#BDC3C7", -- Neutral Gray
    [Profit Margin %] >= 0.20, "#2ECC71", -- Green
    [Profit Margin %] >= 0.10, "#F1C40F", -- Amber Yellow
    "#E74C3C"                             -- Red (Catches < 10% and negative loss margins)
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How to Apply in Power BI Desktop:**
   - Select Visual $\rightarrow$ Format Visual $\rightarrow$ Cell elements / Data colors $\rightarrow$ Click **$fx$** button.
   - Set Format style to **"Field Value"**.
   - Choose field: `[Margin Color Code]`.
   - Power BI instantly renders exact hex colors without needing hardcoded GUI color rules.

2. **The "Negative Profit / Loss" Trap:**
   - If written as `[Profit Margin %] >= 0, "#E74C3C"`, any product running at a net loss (e.g. $-8\%$ margin) fails all conditions and returns `BLANK()`, losing its red alert!
   - Leaving `"#E74C3C"` as the unconditional default fallback guarantees that both low positive margins ($< 10\%$) and negative loss margins get flagged in red.
