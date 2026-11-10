# Q024: Text Concatenation Across Rows (`CONCATENATEX`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 024 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In executive customer profiling cards and drill-through tooltip pages, business users require a concise textual summary of items purchased by a customer. 

Instead of showing a bulky nested table, the UI requires a single, compact, comma-separated string listing distinct product names sorted alphabetically:
$$\text{Expected Output: } \text{"Mechanical Keyboard, Wireless Mouse"}$$

Standard aggregators like `SUM` or `AVERAGE` only handle numeric columns. An interviewer will ask:
> *"1. Which DAX iterator allows row-by-row string concatenation across a table?*
> *2. How do you prevent repeat purchases from duplicating product names in the resulting string?*
> *3. How do you sort the concatenated text alphabetically?"*

---

## 📊 Sample Dataset (`FactSales`)

| OrderID | CustomerID | ProductName |
| :---: | :---: | :--- |
| `101` | **C1** | Wireless Mouse |
| `102` | **C1** | Mechanical Keyboard |
| `103` | **C1** | Wireless Mouse *(Repeat Order)* |
| `104` | **C2** | Gaming Monitor |

---

## 🎯 Expected Output

| CustomerID | Resulting Measure Output |
| :---: | :--- |
| **C1** | `"Mechanical Keyboard, Wireless Mouse"` |
| **C2** | `"Gaming Monitor"` |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Purchased Products List = 
CONCATENATEX(
    VALUES(FactSales[ProductName]),
    FactSales[ProductName],
    ", ",
    FactSales[ProductName],
    ASC
)
```

### Enterprise Guardrail Pattern (Preventing UI Freeze on Large Context):
```dax
Purchased Products List Guarded = 
IF(
    HASONEVALUE(FactSales[CustomerID]),
    CONCATENATEX(
        TOPN(5, VALUES(FactSales[ProductName]), FactSales[ProductName], ASC),
        FactSales[ProductName],
        ", ",
        FactSales[ProductName],
        ASC
    ),
    "Select a single customer to view purchases"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **`VALUE()` vs `VALUES()` (Syntax Distinction):**
   - `VALUE(<Text>)`: Scalar function that converts a numeric string to a number (e.g. `VALUE("100")` $\rightarrow$ `100`).
   - `VALUES(<Column>)`: Table function that returns a 1-column table of distinct values respecting current filter context.

2. **Parameter Anatomy of `CONCATENATEX`:**
   - Parameter 1: `Table` (`VALUES(FactSales[ProductName])`)
   - Parameter 2: `Expression` (`FactSales[ProductName]`)
   - Parameter 3: `Delimiter` (`", "`)
   - Parameter 4: `OrderBy_Expression` (`FactSales[ProductName]`)
   - Parameter 5: `Order` (`ASC` / `DESC`)

3. **Performance & Memory Considerations:**
   - Concatenating thousands of long strings in memory can cause severe memory spikes in the formula engine. Always ensure it evaluates within a narrow filter context (e.g., single customer/order profile card).
