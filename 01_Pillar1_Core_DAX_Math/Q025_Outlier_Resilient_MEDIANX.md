# Q025: Outlier-Resilient Central Tendency (`MEDIAN` & `MEDIANX`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 025 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In retail, real estate, and salary analytics, numerical distributions are frequently right-skewed by rare, extreme outliers (e.g., a single \$50,000 corporate purchase among typical \$30 consumer basket sizes).

Using arithmetic mean (`AVERAGE`) skews executive reporting, creating an illusion of high typical customer spend:
$$\text{Average Spend: } \$10,028 \quad \text{vs} \quad \text{Median Spend: } \$40$$

An interviewer will ask:
> *"1. Why is `AVERAGE` dangerous in skewed transaction data, and which statistical measure solves this in DAX?*
> *2. How do you compute the median when the target metric is an unmaterialized row-by-row calculation ($\text{Quantity} \times \text{UnitPrice}$)?"*

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | Quantity | UnitPrice | Computed Line Total |
| :---: | :---: | :---: | :---: |
| `1` | 1 | \$20 | \$20 |
| `2` | 1 | \$30 | \$30 |
| `3` | 1 | **\$40** | **\$40** $\leftarrow$ *(True 50th Percentile / Median)* |
| `4` | 2 | \$25 | \$50 |
| `5` | 100 | \$500 | **\$50,000** $\leftarrow$ *(Extreme Outlier)* |

---

## 🎯 Statistical Comparison

| Metric | Formula | Output | Business Interpretation |
| :--- | :--- | :---: | :--- |
| **Arithmetic Mean (`AVERAGE`)** | $\frac{20+30+40+50+50000}{5}$ | **\$10,028** | Misleading: Severely distorted by single \$50k purchase. |
| **Median (`MEDIANX`)** | 50th Percentile | **\$40** | Accurate: Represents true typical consumer behavior. |

---

## 💡 Production DAX Solutions

### Row-by-Row Iterator Pattern (`MEDIANX`):
```dax
Median Order Value = 
MEDIANX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

### Direct Physical Column Pattern (`MEDIAN`):
```dax
Median Line Sales = 
MEDIAN(FactSales[LineTotal])
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `MEDIAN` / `MEDIANX` over `AVERAGE` / `AVERAGEX`:**
   - `AVERAGE` is sensitive to data skew and outliers.
   - `MEDIAN` returns the 50th percentile (exact middle value when sorted), providing a reliable representation of central tendency in skewed business domains (e.g. order values, customer lifetime value, delivery turnaround times, compensation).

2. **Even vs Odd Row Count Handling:**
   - If the count of rows is odd, DAX takes the exact middle item.
   - If the count of rows is even, DAX automatically computes the arithmetic mean of the two middle values.

3. **Engine Execution Cost:**
   - Unlike `SUM` or `AVERAGE` which stream values incrementally, `MEDIAN` requires sorting/indexing values to identify the 50th percentile, making it more CPU-intensive on multi-million row tables.
