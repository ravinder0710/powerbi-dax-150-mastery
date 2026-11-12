# Q034: Dynamic Auto-Scaling Currency (`K` / `M` Formatting)

## 📌 Metadata
- **Pillar:** Pillar 2: Logical, Text & Conditional DAX
- **Question Number:** 034 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Executive KPI cards, report headers, and cross-regional dashboards display financial performance spanning vast numeric scales (from local kiosks generating thousands to enterprise global hubs generating millions).

Business users require a dynamic auto-scaling currency measure that displays human-readable suffixes (`K`, `M`) based on the magnitude of the sales number:
- **$\ge ₹ 1,000,000$:** `"₹ 2.5M"`
- **$\ge ₹ 1,000$:** `"₹ 450.5K"`
- **$< ₹ 1,000$:** `"₹ 850"`
- **Blank / Null:** `"₹ 0"`

An interviewer will ask:
> *"1. How do you implement dynamic currency scale formatting (Thousands/Millions) in DAX?*
> *2. Why is caching the base measure inside a Variable (`VAR`) critical for performance in this specific expression?*
> *3. What is the alternative native Power BI feature introduced recently for dynamic format strings?"*

---

## 📦 Available Measures in Model
- `[Total Net Sales]`

---

## 📊 Sample Dataset

| Store / Region | Raw `[Total Net Sales]` | Expected Scaled Display | Suffix Logic |
| :--- | :---: | :---: | :--- |
| **USA National** | ₹ 2,540,000 | `"₹ 2.5M"` | Divided by $1,000,000$ |
| **Regional Hub** | ₹ 450,500 | `"₹ 450.5K"` | Divided by $1,000$ |
| **Local Kiosk** | ₹ 850 | `"₹ 850"` | As-is whole number |
| **Closed Store** | *(BLANK)* | `"₹ 0"` | Blank fallback |

---

## 💡 Production DAX Solutions

### Standard Optimized Pattern with Variable (`VAR`):
```dax
Formatted Sales Scaled = 
VAR SalesVal = [Total Net Sales]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(SalesVal), "₹ 0",
    SalesVal >= 1000000, "₹ " & FORMAT(SalesVal / 1000000, "0.0") & "M",
    SalesVal >= 1000,    "₹ " & FORMAT(SalesVal / 1000, "0.0") & "K",
    "₹ " & FORMAT(SalesVal, "#,##0")
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `VAR` is Mandatory in This Expression:**
   - Without `VAR SalesVal`, the DAX engine evaluates `[Total Net Sales]` up to **6 to 7 times** per row or card query (during `ISBLANK`, `>= 1000000`, `FORMAT`, `>= 1000`, etc.).
   - Using `VAR` evaluates `[Total Net Sales]` **exactly once**, caches the scalar value in memory, and reuses it across all conditions, delivering up to a **7x performance speedup**.

2. **Native Alternative: Dynamic Format Strings:**
   - In modern Power BI Desktop, developers can keep the measure numeric (so it works on chart axes) and apply a **Dynamic Format String** expression directly under Measure Tools $\rightarrow$ Format:
   ```dax
   SWITCH(
       TRUE(),
       SELECTEDMEASURE() >= 1000000, """₹ ""0.0,,\M",
       SELECTEDMEASURE() >= 1000,    """₹ ""0.0,\K",
       """₹ ""#,##0"
   )
   ```
   - *Mentioning this in an interview immediately distinguishes you as an advanced modern Power BI developer!*
