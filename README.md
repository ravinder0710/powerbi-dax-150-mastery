# 📊 Power BI & DAX Interview Mastery — Top 150 Questions Challenge
### (Data Analyst & BI Developer | 0 to 2 Years Experience)

![Power BI Challenge](https://img.shields.io/badge/Power_BI-Top_150_Interview_Challenge-yellow.svg)
![Target Role](https://img.shields.io/badge/Role-Data_Analyst_%7C_0--2_Years-blue.svg)
![Status](https://img.shields.io/badge/Progress-35%2F150_(23.3%25)-brightgreen.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)
![Power BI Desktop](https://img.shields.io/badge/Power_BI_Desktop-100%25_Free-success.svg)

A production-grade, highly focused interview preparation repository containing **150 battle-tested Power BI & DAX questions** specifically curated for **Freshers and 0–2 Years Experience Data Analysts**.

---

## 💻 Environment & Tooling (100% Free Practice)

- **Power BI Desktop:** 100% Free to download and use on Windows (No credit card or Microsoft 365 subscription required).
- **Core Capabilities Covered:** All 150 DAX measures, Data Modeling, Star Schemas, and Power Query transformations in this repository can be fully built and verified inside the free Power BI Desktop application.
- **Enterprise Note:** In production IT environments, report authoring is done locally in Power BI Desktop, while cloud report sharing and scheduled refreshes utilize company-provided Power BI Pro/Premium accounts.

---

## 🗂️ Curriculum & Progress Tracker (35 / 150)

| Pillar | Topic / Area | Target Questions | Completed | Status |
|:---|:---|:---:|:---:|:---|
| **Pillar 1** | **Core DAX Aggregations & Math** (Qs 001 – 030) | 30 | 30 | `[██████████] 100% ✅ Completed` |
| **Pillar 2** | **Logical, Text & Conditional DAX** (Qs 031 – 060) | 30 | 5 | `[██░░░░░░░░] 17%` |
| **Pillar 3** | **Filter Context King (`CALCULATE` & Modifiers)** (Qs 061 – 090) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 4** | **Time Intelligence Masterclass** (Qs 091 – 120) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **Pillar 5** | **Data Modeling, Relationships & Service** (Qs 121 – 150) | 30 | 0 | `[░░░░░░░░░░] 0%` |
| **TOTAL** | **5 Core Pillars** | **150** | **35** | **35 / 150 (23.3%)** |

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
| **Q011** | Max Single Line Sale | Row-by-Row Maximum using `MAXX` Iterator | Amazon, Flipkart, Walmart | [Q011 Solution ↗](01_Pillar1_Core_DAX_Math/Q011_Max_Sale_Value_MAXX.md) |
| **Q012** | Min Paid Sale Value | Zero Exclusion Filtering using `MINX` & `FILTER` | D-Mart, Reliance Fresh, BigBasket | [Q012 Solution ↗](01_Pillar1_Core_DAX_Math/Q012_Min_Paid_Sale_Value_MINX.md) |
| **Q013** | Average Daily Sales (ADS) | Daily Run-Rate using `AVERAGEX` & `VALUES` | Fractal Analytics, Tiger Analytics | [Q013 Solution ↗](01_Pillar1_Core_DAX_Math/Q013_Average_Daily_Sales_AVERAGEX.md) |
| **Q014** | ASP vs AOV Unit Economics | Volume-Weighted Selling Price vs Order Basket | Zara, H&M, Nykaa | [Q014 Solution ↗](01_Pillar1_Core_DAX_Math/Q014_ASP_vs_AOV_Ratios.md) |
| **Q015** | Realized Discount % | Discount Erosion vs Gross MRP Sales | Landmark Group, Aditya Birla Retail | [Q015 Solution ↗](01_Pillar1_Core_DAX_Math/Q015_Realized_Discount_Percentage.md) |
| **Q016** | Safe Null Fallback Display | Eliminating Blanks cleanly with `COALESCE` | Accenture, Deloitte, TCS | [Q016 Solution ↗](01_Pillar1_Core_DAX_Math/Q016_Safe_Null_Handling_COALESCE.md) |
| **Q017** | Incomplete Profile Audit | Data Quality Governance using `COUNTBLANK` | Infosys, Wipro, Cognizant | [Q017 Solution ↗](01_Pillar1_Core_DAX_Math/Q017_Count_Blank_and_Missing_Records.md) |
| **Q018** | Strict Zero vs Blank Audit | Lenient Equality `=` vs Strict Equality `==` | KPMG, EY, PwC | [Q018 Solution ↗](01_Pillar1_Core_DAX_Math/Q018_Strict_Equality_Blank_vs_Zero.md) |
| **Q019** | Omnichannel Revenue Consolidation | Safe Multi-Channel Addition & Blank Auto-Suppression | Croma, Reliance Digital, Apple | [Q019 Solution ↗](01_Pillar1_Core_DAX_Math/Q019_Safe_Omnichannel_Addition_COALESCE.md) |
| **Q020** | Slicer Price Bucketing | Calculated Column vs Measure Architectural Choice | Adobe, Salesforce, PayTM | [Q020 Solution ↗](01_Pillar1_Core_DAX_Math/Q020_Calculated_Column_vs_Measure_Slicer.md) |
| **Q021** | Active Customer Penetration % | Fact vs Dimension Ratio & Conversion Efficiency | Flipkart, Swiggy, Uber | [Q021 Solution ↗](01_Pillar1_Core_DAX_Math/Q021_Active_Customer_Penetration_Ratio.md) |
| **Q022** | Cross-Table Iterator (RELATED) | Row Context Traversal across 1-to-Many Model | Microsoft, Amazon, Infosys | [Q022 Solution ↗](01_Pillar1_Core_DAX_Math/Q022_Multi_Table_Iterator_RELATED.md) |
| **Q023** | Disconnected Table Lookups | Disconnected Schema Lookup (`LOOKUPVALUE` vs `RELATED`) | Google, Meta, Capgemini | [Q023 Solution ↗](01_Pillar1_Core_DAX_Math/Q023_Disconnected_Lookup_LOOKUPVALUE.md) |
| **Q024** | Customer Products Text List | Row-by-Row String Concatenation (`CONCATENATEX`) | LinkedIn, Airbnb, Netflix | [Q024 Solution ↗](01_Pillar1_Core_DAX_Math/Q024_Text_Aggregation_CONCATENATEX.md) |
| **Q025** | Outlier-Resilient Median | Skew Protection using `MEDIAN` & `MEDIANX` | Uber, Zomato, American Express | [Q025 Solution ↗](01_Pillar1_Core_DAX_Math/Q025_Outlier_Resilient_MEDIANX.md) |
| **Q026** | Financial & Multiple Rounding | Precision & Cash Multiples (`ROUND` vs `MROUND`) | PhonePe, Paytm, HDFC Bank | [Q026 Solution ↗](01_Pillar1_Core_DAX_Math/Q026_Financial_Rounding_ROUND_MROUND.md) |
| **Q027** | Sub-Category Parent % | Hierarchical Parent Subtotal via `ALLEXCEPT` | Target, Walmart, Best Buy | [Q027 Solution ↗](01_Pillar1_Core_DAX_Math/Q027_SubCategory_Percentage_ALLEXCEPT.md) |
| **Q028** | Visual Sliced Contribution % | Dynamic Visual Total 100% via `ALLSELECTED` | Spotify, Netflix, Myntra | [Q028 Solution ↗](01_Pillar1_Core_DAX_Math/Q028_Visual_Total_ALLSELECTED.md) |
| **Q029** | Top N Aggregation | Dynamic Best-Seller KPI via `TOPN` & `CALCULATE` | Amazon, Apple, Nike | [Q029 Solution ↗](01_Pillar1_Core_DAX_Math/Q029_TopN_Aggregation_CALCULATE.md) |
| **Q030** | Top N Portfolio Concentration % | Pareto 80/20 Analysis & Measure Branching | McKinsey, BCG, Bain | [Q030 Solution ↗](01_Pillar1_Core_DAX_Math/Q030_TopN_Portfolio_Concentration.md) |
| **Q031** | KPI Target Status Badge | Conditional Status Chips via `SWITCH(TRUE())` | Uber, Microsoft, Deloitte | [Q031 Solution ↗](02_Pillar2_Logical_Conditional/Q031_KPI_Status_Badge_SWITCH_TRUE.md) |
| **Q032** | Dynamic Color Hex Formatting | Dynamic Background & Font Colors via Hex Codes | Airbnb, Spotify, Target | [Q032 Solution ↗](02_Pillar2_Logical_Conditional/Q032_Dynamic_Color_Formatting_Hex.md) |
| **Q033** | Dynamic Arrow Indicators & Variance | KPI Unicode Arrows & Text Formatting via `FORMAT` | American Express, Barclays, VISA | [Q033 Solution ↗](02_Pillar2_Logical_Conditional/Q033_Unicode_Arrows_Trend_KPI.md) |
| **Q034** | Dynamic Auto-Scaling Currency | Human-Readable K/M Currency Suffixes via `FORMAT` | Bloomberg, Goldman Sachs, Stripe | [Q034 Solution ↗](02_Pillar2_Logical_Conditional/Q034_Dynamic_Auto_Scaling_Currency.md) |
| **Q035** | Multi-Criteria Customer Segmentation | Compound Boolean `&&` Logic via `SWITCH(TRUE())` | Amazon, Flipkart, Swiggy | [Q035 Solution ↗](02_Pillar2_Logical_Conditional/Q035_Multi_Criteria_Customer_Segmentation.md) |

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
