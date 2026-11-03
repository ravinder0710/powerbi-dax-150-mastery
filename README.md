# 📊 Power BI & DAX Interview Mastery — 450 Questions Challenge
### (Data Analyst, BI Developer & Power BI Specialist | Zero to Hero)

![Power BI Challenge](https://img.shields.io/badge/Power_BI-450_DAX_Challenge-yellow.svg)
![Target Role](https://img.shields.io/badge/Role-Data_Analyst_%7C_BI_Developer-blue.svg)
![Status](https://img.shields.io/badge/Status-In_Progress-brightgreen.svg)

A production-grade, battle-tested repository containing **450 curated Power BI & DAX interview questions** designed strictly for clearing technical rounds. Covering everything from scalar aggregations and row-by-row iterators to complex filter context transitions, semi-additive measures, time intelligence, dynamic RLS, and VertiPaq engine tuning.

---

## 🗂️ Curriculum & Progress Tracker (8 / 450)

| Tier | Category | Questions Target | Completed | Progress Status |
|:---|:---|:---:|:---:|:---|
| **1. Basic** | 01. Basic Easy (Aggregations, Math, Scalar Measures) | 50 | 8 | `[█░░░░░░░░░] 16%` |
| | 02. Basic Medium (Logical, DIVIDE, Basic CALCULATE) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| | 03. Basic Hard (Iterators: SUMX, AVERAGEX, RELATED) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| **2. Intermediate** | 01. Intermediate Easy (ALL, ALLEXCEPT, ALLSELECTED, Slicers) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| | 02. Intermediate Medium (YoY, MoM, Time Intelligence, USERELATIONSHIP) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| | 03. Intermediate Hard (RANKX, TOPN, Disconnected Tables, Semi-additive) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| **3. Advance** | 01. Advance Easy (Context Transition, KEEPFILTERS, TREATAS) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| | 02. Advance Medium (Cohort, Churn, Dynamic RLS, WINDOW/OFFSET) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| | 03. Advance Hard (Many-to-Many DAX, Hierarchies, Engine Tuning) | 50 | 0 | `[░░░░░░░░░░] 0%` |
| **TOTAL** | **9 Categories** | **450** | **0** | **0 / 450** |

---

## 📁 Repository Structure

```
powerbi-dax-450-mastery/
├── 01_Basic/
│   ├── 01_Basic_Easy/        # (Q001 - Q050) Scalar Aggregations, Math, Measures vs Calc Columns
│   ├── 02_Basic_Medium/      # (Q051 - Q100) IF, SWITCH, DIVIDE, BLANK, Basic CALCULATE
│   └── 03_Basic_Hard/        # (Q101 - Q150) Iterators (SUMX, AVERAGEX), RELATED, RELATEDTABLE
├── 02_Intermediate/
│   ├── 01_Inter_Easy/        # (Q151 - Q200) ALL, ALLEXCEPT, ALLSELECTED, % of Total
│   ├── 02_Inter_Medium/      # (Q201 - Q250) YoY, MoM, DATESYTD, SAMEPERIODLASTYEAR, USERELATIONSHIP
│   └── 03_Inter_Hard/        # (Q251 - Q300) RANKX, TOPN, Dynamic Metric Switchers, Semi-additive
└── 03_Advance/
    ├── 01_Adv_Easy/          # (Q301 - Q350) Context Transition, KEEPFILTERS, Expanded Tables, TREATAS
    ├── 02_Adv_Medium/        # (Q351 - Q400) Customer Churn, New vs Repeat, Dynamic RLS, Window DAX
    └── 03_Adv_Hard/          # (Q401 - Q450) Many-to-Many Bridge vs DAX, Parent-Child, DAX Studio Tuning
```

---

## 🏢 Standard Star Schema Definition

All questions in this repository are based on an enterprise standard Retail / E-commerce star schema:

* **`DimProduct`** (`ProductID`, `ProductName`, `Category`, `SubCategory`, `StandardCost`, `ListPrice`)
* **`DimCustomer`** (`CustomerID`, `CustomerName`, `Gender`, `Country`, `City`, `CustomerType`)
* **`DimDate`** (`Date`, `DateKey`, `Year`, `MonthNum`, `MonthName`, `Quarter`, `DayOfWeek`)
* **`DimStore`** (`StoreID`, `StoreName`, `Region`, `Country`)
* **`FactSales`** (`OrderNumber`, `OrderDateKey`, `ShipDateKey`, `ProductID`, `CustomerID`, `StoreID`, `Quantity`, `UnitPrice`, `DiscountAmount`, `TotalCost`)
* **`FactBudget`** (`DateKey`, `Category`, `BudgetAmount`)

---

## 🛠️ File Format Standard

Every question file is documented with:
1. **Business Problem & Scenario Recap**
2. **Mock Data Sample (5–7 rows)**
3. **Visual / Display Matrix Expectation**
4. **Optimized DAX Measure Solution**
5. **Interviewer Defense & Common Traps** (Why this function? Filter context impact? Engine cost?)
