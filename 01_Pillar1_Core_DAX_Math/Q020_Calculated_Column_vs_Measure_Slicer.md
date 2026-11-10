# Q020: Calculated Column vs Measure — Slicer Bucketing & Categorization

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 020 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
A retail category manager needs to filter sales visuals by product price bands:
- **Budget:** Products under \$20
- **Mid-Range:** Products between \$20 and \$100
- **Premium:** Products over \$100

The dashboard requires an interactive **Slicer (Dropdown / Tile visual)** allowing end users to click any price segment and instantly cross-filter all report pages.

An interviewer will ask:
> *"Should this price categorization be created as a **Calculated Column** or a **Measure**? Why is one mandatory over the other, and what DAX expression would you write?"*

---

## 📊 Sample Dataset (`DimProduct`)

| ProductKey | ProductName | UnitPrice | Expected Category |
| :--- | :--- | :--- | :--- |
| `101` | Basic Mouse | \$15 | Budget |
| `102` | Mechanical Keyboard | \$75 | Mid-Range |
| `103` | 4K Gaming Monitor | \$350 | Premium |
| `104` | USB Cable | \$8 | Budget |
| `105` | Ergonomic Chair | \$100 | Mid-Range |

---

## 🎯 Architectural Decision: Calculated Column vs Measure

| Dimension | Calculated Column (Selected ✅) | Measure (Not Applicable ❌) |
| :--- | :--- | :--- |
| **Slicer Compatibility** | ✅ **Supported:** Generates discrete values stored in data model. | ❌ **Unsupported:** Cannot be dragged into Slicers, Rows, or Columns headers. |
| **Evaluation Timing** | During Data Refresh (model processing). | On-the-Fly during user visual interaction (Query time). |
| **Storage / RAM** | Consumes VertiPaq RAM & disk file size. | Zero persistent storage (RAM used only temporarily during calculation). |
| **Evaluation Context** | Operates in **Row Context** (evaluates row-by-row). | Operates in **Filter Context** (summarizes aggregated subsets). |

### 🚨 Architectural Rule:
> **Measures return single aggregated scalar values and cannot slice or partition data.** Whenever a field is required as a **Slicer, Chart Axis, Legend, or Matrix Header**, a **Calculated Column** (or upstream Power Query / SQL transformation) is strictly required.

---

## 💡 Production DAX Solutions

### Calculated Column Definition (`DimProduct` Table):
```dax
Price Category = 
SWITCH(
    TRUE(),
    DimProduct[UnitPrice] < 20, "Budget",
    DimProduct[UnitPrice] <= 100, "Mid-Range",
    DimProduct[UnitPrice] > 100, "Premium",
    "Unknown"
)
```

### Alternative using Nested `IF`:
```dax
Price Category (IF) = 
IF(
    DimProduct[UnitPrice] < 20,
    "Budget",
    IF(
        DimProduct[UnitPrice] <= 100,
        "Mid-Range",
        IF(
            DimProduct[UnitPrice] > 100,
            "Premium",
            "Unknown"
        )
    )
)
```
*(Note: `SWITCH(TRUE(), ...)` is universally preferred in production for readability and maintainability.)*

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `SWITCH(TRUE(), ...)` over Nested `IF`?**
   - Clean, readable ladder evaluation structure.
   - Evaluates sequentially from top to bottom and exits immediately on the first matching condition.

2. **Best Practice for Scalability:**
   - While DAX Calculated Columns work seamlessly for bucketing, enterprise best practice recommends creating static category buckets as far upstream as possible (SQL View or Power Query conditional column) to optimize VertiPaq compression.
