# Q027: Sub-Category Contribution within Parent Category (`ALLEXCEPT`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 027 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In hierarchical reporting (e.g., Matrix visuals breaking down `Category` into `SubCategory`), stakeholders require percentage of parent contribution ratios:
- Rather than measuring what % of **Total Enterprise Revenue** a sub-category represents, they want to evaluate what % of **its parent Category** it delivers (e.g., Laptops sales divided strictly by total Electronics sales).

The data model features a standard Star Schema:
- `DimProduct` (`ProductKey`, `Category`, `SubCategory`, `ProductName`)
- `FactSales` (`OrderID`, `ProductKey`, `SalesAmount`)

An interviewer will ask:
> *"1. Why does `ALL(DimProduct)` fail to calculate percentage of category, and how does `ALLEXCEPT` solve it?*
> *2. If an end user slices the report by `DimDate[Year] = 2024`, does `ALLEXCEPT(DimProduct, DimProduct[Category])` remove the year filter? Why or why not?"*

---

## 📊 Sample Dataset

### `DimProduct` (Dimension Table)
| ProductKey | Category | SubCategory | ProductName |
| :---: | :--- | :--- | :--- |
| `101` | **Electronics** | Phones | iPhone 15 |
| `102` | **Electronics** | Laptops | Dell XPS |
| `103` | **Furniture** | Chairs | Office Chair |
| `104` | **Furniture** | Tables | Dining Table |

### `FactSales` (Fact Table)
| OrderID | ProductKey | SalesAmount |
| :---: | :---: | :---: |
| `1` | 101 *(Phones)* | ₹ 60,000 |
| `2` | 102 *(Laptops)* | ₹ 40,000 |
| `3` | 103 *(Chairs)* | ₹ 20,000 |
| `4` | 104 *(Tables)* | ₹ 30,000 |

---

## 🎯 Expected Output (Matrix Visual)

| Category | SubCategory | SubCategory Sales | Category Group Total | Expected `% of Category Sales` |
| :--- | :--- | :---: | :---: | :---: |
| **Electronics** | Phones | ₹ 60,000 | ₹ 1,00,000 | **60.0%** ($\frac{60k}{100k}$) |
| **Electronics** | Laptops | ₹ 40,000 | ₹ 1,00,000 | **40.0%** ($\frac{40k}{100k}$) |
| **Furniture** | Chairs | ₹ 20,000 | ₹ 50,000 | **40.0%** ($\frac{20k}{50k}$) |
| **Furniture** | Tables | ₹ 30,000 | ₹ 50,000 | **60.0%** ($\frac{30k}{50k}$) |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
SubCategory % of Category Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE(
        [Total Net Sales],
        ALLEXCEPT(DimProduct, DimProduct[Category])
    ),
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **How `ALLEXCEPT` Works Internally:**
   - `ALLEXCEPT(DimProduct, DimProduct[Category])` acts as a targeted filter remover.
   - It strips away all existing filters on `DimProduct[SubCategory]`, `DimProduct[ProductName]`, and `DimProduct[ProductKey]`, while explicitly **preserving** the current filter on `DimProduct[Category]`.
   - As a result, the denominator dynamically evaluates total sales for that specific Category row.

2. **Cross-Table Filter Preservation (Crucial Interview Trap):**
   - `ALLEXCEPT` only removes filters from the table specified in its first argument (`DimProduct`).
   - Filters originating from other tables in the Star Schema (e.g., `DimDate[Year] = 2024` or `DimStore[Region] = 'North'`) remain **100% active** on both numerator and denominator.
   - This ensures that when an executive filters by Year 2024, the parent category denominator correctly reflects only Year 2024 Category sales.
