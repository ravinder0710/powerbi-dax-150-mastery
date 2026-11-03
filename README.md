# 📊 Power BI & DAX Interview Mastery — Top 100 Questions Challenge
### (Data Analyst & BI Developer | 0 to 2 Years Experience)

![Power BI Challenge](https://img.shields.io/badge/Power_BI-Top_100_Interview_Questions-yellow.svg)
![Target Role](https://img.shields.io/badge/Role-Data_Analyst_%7C_0--2_Years-blue.svg)
![Status](https://img.shields.io/badge/Progress-10%2F100_(10%25)-brightgreen.svg)

A production-grade, highly focused interview preparation repository containing **100 high-yield Power BI & DAX questions** specifically curated for **Freshers and 0–2 Years Experience Data Analysts**.

---

## 🗂️ Curriculum & Progress Tracker (10 / 100)

| Pillar | Topic / Area | Target Questions | Completed | Status |
|:---|:---|:---:|:---:|:---|
| **Pillar 1** | **Core DAX Aggregations & Iterators** (`SUM`, `SUMX`, `DIVIDE`, `COUNTROWS`, Math) | 20 | 10 | `[█████░░░░░] 50%` |
| **Pillar 2** | **Logical & Conditional DAX** (`IF`, `SWITCH(TRUE())`, `BLANK`, `ISBLANK`) | 15 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 3** | **Filter Context King** (`CALCULATE`, `ALL`, `ALLEXCEPT`, `ALLSELECTED`, %) | 25 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 4** | **Time Intelligence Masterclass** (YTD, MTD, `SAMEPERIODLASTYEAR`, YoY Growth %) | 20 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 5** | **Data Modeling, Relationships & Service** (Star Schema, Inactive Joins, RLS) | 20 | 0 | `[░░░░░░░░░░] 0%` |
| **TOTAL** | **5 Core Pillars** | **100** | **10** | **10 / 100 (10%)** |

---

## 📁 Repository Structure

```
powerbi-dax-450-mastery/
├── .git/
├── .gitignore
├── README.md                          # 100-Question Tracker & Architecture
├── INTERVIEW_QUICK_REVISION_NOTES.md  # 1-Liner Interview Punchlines & Traps
├── 01_Pillar1_Core_DAX_Math/          # (Q001 - Q020)
├── 02_Pillar2_Logical_Conditional/    # (Q021 - Q035)
├── 03_Pillar3_Filter_Context/         # (Q036 - Q060)
├── 04_Pillar4_Time_Intelligence/      # (Q061 - Q080)
└── 05_Pillar5_Modeling_Service/       # (Q081 - Q100)
```

---

## 🏢 Standard Star Schema

- **`DimProduct`**: `ProductID`, `ProductName`, `Category`, `SubCategory`, `StandardCost`, `ListPrice`
- **`DimCustomer`**: `CustomerID`, `CustomerName`, `Gender`, `Country`, `City`, `CustomerType`
- **`DimDate`**: `Date`, `DateKey`, `Year`, `MonthNum`, `MonthName`, `Quarter`, `DayOfWeek`
- **`DimStore`**: `StoreID`, `StoreName`, `Region`, `Country`
- **`FactSales`**: `OrderNumber`, `OrderDateKey`, `ShipDateKey`, `ProductID`, `CustomerID`, `StoreID`, `Quantity`, `UnitPrice`, `DiscountAmount`, `TotalCost`
