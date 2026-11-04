# 📊 Power BI & DAX Interview Mastery — Top 150 Questions Challenge
### (Data Analyst & BI Developer | 0 to 2 Years Experience)

![Power BI Challenge](https://img.shields.io/badge/Power_BI-Top_150_Interview_Challenge-yellow.svg)
![Target Role](https://img.shields.io/badge/Role-Data_Analyst_%7C_0--2_Years-blue.svg)
![Status](https://img.shields.io/badge/Progress-10%2F150_(6.7%25)-brightgreen.svg)

A production-grade, highly focused interview preparation repository containing **150 battle-tested Power BI & DAX questions** specifically curated for **Freshers and 0–2 Years Experience Data Analysts**.

---

## 🗂️ Curriculum & Progress Tracker (4 / 150)

| Pillar | Topic / Area | Target Questions | Completed | Status |
|:---|:---|:---:|:---:|:---|
| **Pillar 1** | **Core DAX Aggregations & Math** (Qs 001 – 030) | 30 | 4 | `[█░░░░░░░░░] 13%` |
| **Pillar 2** | **Logical, Text & Conditional DAX** (Qs 031 – 060) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 3** | **Filter Context King (`CALCULATE` & Modifiers)** (Qs 061 – 090) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 4** | **Time Intelligence Masterclass** (Qs 091 – 120) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 5** | **Data Modeling, Relationships & Service** (Qs 121 – 150) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **TOTAL** | **5 Core Pillars** | **150** | **4** | **4 / 150 (2.7%)** |

---

## 📁 Repository Structure

```
powerbi-dax-150-mastery/
├── .git/
├── .gitignore
├── README.md                          # 150-Question Tracker & Architecture
├── INTERVIEW_QUICK_REVISION_NOTES.md  # 1-Liner Interview Punchlines & Traps
├── 01_Pillar1_Core_DAX_Math/          # (Q001 - Q030) Aggregations, Iterators, Math
├── 02_Pillar2_Logical_Conditional/    # (Q031 - Q060) IF, SWITCH, Formatting, Badges
├── 03_Pillar3_Filter_Context/         # (Q061 - Q090) CALCULATE, ALL, ALLEXCEPT, ALLSELECTED
├── 04_Pillar4_Time_Intelligence/      # (Q091 - Q120) YTD, MTD, Prior Year, YoY Growth %
└── 05_Pillar5_Modeling_Service/       # (Q121 - Q150) Star Schema, USERELATIONSHIP, RLS
```

---

## 🏢 Standard Star Schema

- **`DimProduct`**: `ProductID`, `ProductName`, `Category`, `SubCategory`, `StandardCost`, `ListPrice`
- **`DimCustomer`**: `CustomerID`, `CustomerName`, `Gender`, `Country`, `City`, `CustomerType`
- **`DimDate`**: `Date`, `DateKey`, `Year`, `MonthNum`, `MonthName`, `Quarter`, `DayOfWeek`
- **`DimStore`**: `StoreID`, `StoreName`, `Region`, `Country`
- **`FactSales`**: `OrderNumber`, `OrderDateKey`, `ShipDateKey`, `ProductID`, `CustomerID`, `StoreID`, `Quantity`, `UnitPrice`, `DiscountAmount`, `TotalCost`
