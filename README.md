# 📊 Power BI & DAX Interview Mastery — Top 150 Questions Challenge
### (Data Analyst & BI Developer | 0 to 2 Years Experience)

![Power BI Challenge](https://img.shields.io/badge/Power_BI-Top_150_Interview_Challenge-yellow.svg)
![Target Role](https://img.shields.io/badge/Role-Data_Analyst_%7C_0--2_Years-blue.svg)
![Status](https://img.shields.io/badge/Progress-10%2F150_(6.7%25)-brightgreen.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![Power BI Desktop](https://img.shields.io/badge/Power_BI_Desktop-100%25_Free-success.svg)

A production-grade, highly focused interview preparation repository containing **150 battle-tested Power BI & DAX questions** specifically curated for **Freshers and 0–2 Years Experience Data Analysts**.

---

## 💻 Environment & Tooling (100% Free Practice)

- **Power BI Desktop:** 100% Free to download and use on Windows (No credit card or Microsoft 365 subscription required).
- **Core Capabilities Covered:** All 150 DAX measures, Data Modeling, Star Schemas, and Power Query transformations in this repository can be fully built and verified inside the free Power BI Desktop application.
- **Enterprise Note:** In production IT environments, report authoring is done locally in Power BI Desktop, while cloud report sharing and scheduled refreshes utilize company-provided Power BI Pro/Premium accounts.

---

## 🗂️ Curriculum & Progress Tracker (10 / 150)

| Pillar | Topic / Area | Target Questions | Completed | Status |
|:---|:---|:---:|:---:|:---|
| **Pillar 1** | **Core DAX Aggregations & Math** (Qs 001 – 030) | 30 | 10 | `[███░░░░░░░] 33%` |
| **Pillar 2** | **Logical, Text & Conditional DAX** (Qs 031 – 060) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 3** | **Filter Context King (`CALCULATE` & Modifiers)** (Qs 061 – 090) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 4** | **Time Intelligence Masterclass** (Qs 091 – 120) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 5** | **Data Modeling, Relationships & Service** (Qs 121 – 150) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **TOTAL** | **5 Core Pillars** | **150** | **10** | **10 / 150 (6.7%)** |

---

## 📖 Solved Questions Index

| # | Question Title | Topic / Concept | Company Tags | Solution Link |
|:---:|:---|:---|:---|:---:|
| **Q001** | Total Net Sales Revenue | Row-by-Row Iterator (`SUMX`) vs Simple `SUM` | TCS, Infosys, Accenture | [Q001 Solution ↗](01_Pillar1_Core_DAX_Math/Q001_Total_Net_Sales_SUMX.md) |
| **Q002** | COGS & Gross Profit | Measure Branching & Reusability (DRY Principle) | Deloitte, EY, KPMG | [Q002 Solution ↗](01_Pillar1_Core_DAX_Math/Q002_Total_Cost_and_Profit_Measure_Branching.md) |
| **Q003** | Safe Margin Calculation | Division by Zero Protection (`DIVIDE` vs `/`) | Amazon, Flipkart, Walmart | [Q003 Solution ↗](01_Pillar1_Core_DAX_Math/Q003_Profit_Margin_DIVIDE.md) |
| **Q004** | Distinct Order Count | Order Grain vs Row Count (`DISTINCTCOUNT` vs `COUNTROWS`) | Swiggy, Zomato, Zepto | [Q004 Solution ↗](01_Pillar1_Core_DAX_Math/Q004_Distinct_Order_Count.md) |
| **Q005** | Average Order Value (AOV) & UPT | Multi-Item Basket Ratios & `AVERAGE` Grain Trap | Target, Reliance Retail, Croma | [Q005 Solution ↗](01_Pillar1_Core_DAX_Math/Q005_AOV_and_UPT_Ratios.md) |
| **Q006** | Freeze Category KPI | Filter Context Modification via `CALCULATE()` | Fractal Analytics, Tiger Analytics | [Q006 Solution ↗](01_Pillar1_Core_DAX_Math/Q006_Calculate_Basic_Filter.md) |
| **Q007** | Cross-Dimensional Sales | Multi-Table AND Filtering (Comma `,` vs `&&`) | Capgemini, Cognizant, Wipro | [Q007 Solution ↗](01_Pillar1_Core_DAX_Math/Q007_Calculate_Multiple_Filters_AND.md) |
| **Q008** | Lifestyle Bundled Revenue | Set Membership & Table Constructor (`IN` with `{}`) | Myntra, Nykaa, Meesho | [Q008 Solution ↗](01_Pillar1_Core_DAX_Math/Q008_Calculate_Logical_OR_IN.md) |
| **Q009** | Universal Enterprise Benchmark | Removing Filters using `ALL(Table)` vs `ALL(Column)` | Microsoft, Google, Uber | [Q009 Solution ↗](01_Pillar1_Core_DAX_Math/Q009_Calculate_ALL_Clear_Filters.md) |
| **Q010** | Category Contribution Ratio | Pareto Analysis & Percentage of Total (`% of Total`) | McKinsey, BCG, Bain Analytics | [Q010 Solution ↗](01_Pillar1_Core_DAX_Math/Q010_Percentage_of_Total_Sales.md) |

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
