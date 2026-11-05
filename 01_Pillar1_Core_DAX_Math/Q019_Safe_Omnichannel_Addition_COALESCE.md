# Q019: Safe Omnichannel Mathematical Addition (COALESCE & Variables)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 019 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
Omnichannel retail enterprises generate revenue through two independent distribution channels:
1. Physical Retail Store Sales (`[Store Net Sales]`)
2. E-Commerce Web Delivery (`[Online Net Sales]`)

Certain regional markets only operate a physical store without online delivery (returning `BLANK()`), while remote hubs operate online-only without a store. Direct scalar addition must safely evaluate combined revenue without dropping single-channel active cities.

$$\text{Combined Sales} = \text{COALESCE}(\text{Store Sales}, 0) + \text{COALESCE}(\text{Online Sales}, 0)$$

---

## 📊 Sample Dataset (`DimStore`)

| StoreCity | Store Net Sales | Online Net Sales | Expected Combined Total |
| :--- | :--- | :--- | :--- |
| Delhi | ₹ 1,500 | ₹ 800 | ₹ 2,300 |
| Mumbai | ₹ 2,000 | ₹ 1,200 | ₹ 3,200 |
| Shimla | ₹ 900 | *(BLANK)* | ₹ 900 |
| Remote Hub | *(BLANK)* | ₹ 1,100 | ₹ 1,100 |

---

## 🎯 Expected Output

| StoreCity | Combined Sales |
| :--- | :--- |
| Delhi | ₹ 2,300 |
| Mumbai | ₹ 3,200 |
| Shimla | ₹ 900 |
| Remote Hub | ₹ 1,100 |

---

## 💡 Production DAX Solutions

### Standard Production Approach:
```dax
Total Omnichannel Sales = 
COALESCE([Store Net Sales], 0) + COALESCE([Online Net Sales], 0)
```

### Advanced Senior Pattern (Preserving Blank Auto-Suppression):
```dax
Total Omnichannel Sales = 
VAR StoreSales = [Store Net Sales]
VAR OnlineSales = [Online Net Sales]
RETURN
IF(
    ISBLANK(StoreSales) && ISBLANK(OnlineSales),
    BLANK(),
    COALESCE(StoreSales, 0) + COALESCE(OnlineSales, 0)
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Use Variables (`VAR`):**
   - Storing measures in variables caches the scalar result during query execution, avoiding repetitive measure evaluation and improving execution speed.
2. **Preserving Blank Suppression:**
   - If a city has neither store nor online sales, returning `BLANK()` allows Power BI visuals to automatically suppress the row, maintaining visual cleanliness.
