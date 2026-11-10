# Q021: Active Customer Penetration % (Customer Conversion Ratio)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 021 / 150
- **Difficulty:** Basic
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
A retail CRM marketing director wants to monitor customer acquisition and activation efficiency via an Executive KPI Card:
- What percentage of registered customer base has actively placed at least one purchase?
- Which regions have high registration counts but low purchase activation?

The data model contains:
1. `DimCustomer` (Dimension Table: Master list of all registered users)
2. `FactSales` (Fact Table: Historical transactional orders)

$$\text{Active Customer Penetration \%} = \frac{\text{Distinct Purchasing Customers in FactSales}}{\text{Total Registered Customers in DimCustomer}}$$

---

## 📊 Sample Dataset

### `DimCustomer` (Customer Master Table)
| CustomerKey | CustomerName | City | Country |
| :---: | :--- | :--- | :--- |
| `1` | Amit Sharma | Delhi | India |
| `2` | Priya Singh | Mumbai | India |
| `3` | Rahul Verma | Bengaluru | India |
| `4` | Sneha Patel | Ahmedabad | India |
| `5` | Vikram Rao | Hyderabad | India |

### `FactSales` (Order Transactions)
| OrderID | CustomerKey | ProductKey | OrderAmount |
| :---: | :---: | :---: | :---: |
| `1001` | **1** | 201 | ₹ 1,500 |
| `1002` | **1** | 202 | ₹ 800 |
| `1003` | **3** | 205 | ₹ 2,200 |
| `1004` | **1** | 201 | ₹ 450 |

---

## 🎯 Expected Output

| Metric | Calculation | Formatted Output |
| :--- | :--- | :--- |
| **Purchasing Customers** | `DISTINCTCOUNT(FactSales[CustomerKey])` | 2 |
| **Total Registered Customers** | `COUNTROWS(DimCustomer)` | 5 |
| **Active Customer Penetration %** | `2 / 5` | **40.0%** |

---

## 💡 Production DAX Solutions

### Standard Production Measure:
```dax
Active Customer % = 
DIVIDE(
    DISTINCTCOUNT(FactSales[CustomerKey]),
    COUNTROWS(DimCustomer),
    0
)
```

### Best Practice: Measure Branching (DRY Principle):
```dax
-- Base Measure 1: Distinct Purchasing Customers
[Purchasing Customers] = DISTINCTCOUNT(FactSales[CustomerKey])

-- Base Measure 2: Total Registered Customers
[Total Registered Customers] = COUNTROWS(DimCustomer)

-- KPI Ratio Measure
[Active Customer Penetration %] = 
DIVIDE(
    [Purchasing Customers],
    [Total Registered Customers],
    0
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why `DISTINCTCOUNT` on `FactSales` vs `DimCustomer`?**
   - In `FactSales`, repeat customers create multiple rows for the same `CustomerKey`. `DISTINCTCOUNT` removes duplicates to return true active buyers.
   - In `DimCustomer`, `CustomerKey` is the primary key (1 row per customer), so `COUNTROWS(DimCustomer)` is the fastest VertiPaq operation.

2. **Slicer Impact (Dynamic Context):**
   - When a user filters by `City = 'Delhi'`, both numerator and denominator filter dynamically, giving the local conversion rate.
   - If an interviewer asks: *"What if the business wants the global benchmark regardless of slicers?"*
     $\rightarrow$ Wrap the denominator with `ALL(DimCustomer)`.
