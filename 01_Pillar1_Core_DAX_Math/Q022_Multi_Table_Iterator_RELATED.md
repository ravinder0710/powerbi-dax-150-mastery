# Q022: Multi-Table Iterator & The `RELATED` Function

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 022 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In an enterprise retail data warehouse following standard Star Schema design, cost rates and list prices are stored strictly in the dimension table (`DimProduct`) to avoid data redundancy in the high-volume transaction table (`FactSales`).

The tables are joined by a 1-to-Many active relationship on `ProductKey`:
- `DimProduct[ProductKey]` (1 Side - Primary Key)
- `FactSales[ProductKey]` (Many Side - Foreign Key)

A finance analyst needs to compute total cost of goods sold row-by-row:
$$\text{Total Cost} = \sum (\text{FactSales[Quantity]} \times \text{DimProduct[StandardCost]})$$

An interviewer will ask:
> *"Why does `SUMX(FactSales, FactSales[Quantity] * DimProduct[StandardCost])` throw a syntax error in DAX, and how does `RELATED` resolve it?"*

---

## 📊 Sample Dataset

### `DimProduct` (Dimension - "1" Side)
| ProductKey | ProductName | StandardCost |
| :---: | :--- | :---: |
| `101` | Wireless Mouse | ₹ 200 |
| `102` | Gaming Keyboard | ₹ 800 |

### `FactSales` (Fact - "Many" Side)
| OrderID | ProductKey | Quantity |
| :---: | :---: | :---: |
| `501` | **101** | 3 |
| `502` | **102** | 2 |
| `503` | **101** | 1 |

---

## 🎯 Expected Output

| OrderID | Quantity | Looked-up StandardCost | Extended Cost |
| :---: | :---: | :---: | :---: |
| `501` | 3 | ₹ 200 | ₹ 600 |
| `502` | 2 | ₹ 800 | ₹ 1,600 |
| `503` | 1 | ₹ 200 | ₹ 200 |
| **Total** | | | **₹ 2,400** |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Total Cost = 
SUMX(
    FactSales,
    FactSales[Quantity] * RELATED(DimProduct[StandardCost])
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why is `RELATED` Mandatory? (Row Context Limitation):**
   - When `SUMX` iterates over `FactSales`, it creates a **Row Context** that is strictly isolated to the columns of `FactSales`.
   - Even though a relationship exists in the model, Row Context does **not** automatically propagate across relationships!
   - `RELATED()` instructs the DAX engine to follow the existing active relationship from the **Many** side to the **One** side to retrieve the scalar value.

2. **`RELATED` vs `RELATEDTABLE` (Classic Interview Question):**
   - **`RELATED(<Column>)`**: Traverses from **Many $\rightarrow$ One**. Returns a **single scalar value** (because each sales row matches exactly one product).
   - **`RELATEDTABLE(<Table>)`**: Traverses from **One $\rightarrow$ Many**. Returns an entire **table of related rows** (because one product matches multiple sales transactions, e.g. `COUNTROWS(RELATEDTABLE(FactSales))`).

3. **Performance Optimization:**
   - If the fact table has tens of millions of rows, iterating across relationships using `RELATED` inside `SUMX` can be CPU-intensive.
   - Best practice for ultra-large models: Merge or pre-calculate UnitCost into the Fact table upstream during ETL/Power Query if query performance bottlenecks occur.
