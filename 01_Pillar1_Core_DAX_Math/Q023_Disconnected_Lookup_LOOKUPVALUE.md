# Q023: Disconnected Table Lookups (`LOOKUPVALUE` vs `RELATED`)

## 📌 Metadata
- **Pillar:** Pillar 1: Core DAX Aggregations & Math
- **Question Number:** 023 / 150
- **Difficulty:** Basic to Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
In many corporate analytics architectures, planning, benchmark, or currency conversion tables are maintained in disconnected models (tables without physical foreign key relationships) to prevent circular dependencies or complex schema ambiguity.

A retail finance department maintains a separate benchmark table `SalesTargets` containing target margin percentages by product category. No physical relationship is drawn between `SalesTargets` and the transactional `FactSales` table.

An interviewer will ask:
> *"1. Why does `RELATED()` fail when attempting to fetch values from a disconnected table?*
> *2. What function replaces it, and what happens if multiple conflicting matches exist in the lookup table?"*

---

## 📊 Sample Dataset

### `SalesTargets` (Disconnected Table - No Model Relationship)
| ProductCategory | TargetMarginRate |
| :--- | :---: |
| Electronics | 0.25 |
| Furniture | 0.15 |
| Clothing | 0.30 |

### `FactSales` (Order Transactions)
| OrderID | ProductCategory | SaleAmount | Looked-up Target Margin Rate |
| :---: | :--- | :---: | :---: |
| `101` | Electronics | ₹ 1,000 | **0.25** |
| `102` | Furniture | ₹ 500 | **0.15** |
| `103` | Electronics | ₹ 2,000 | **0.25** |

---

## 🎯 Architectural Comparison: `RELATED` vs `LOOKUPVALUE`

| Feature | `RELATED()` | `LOOKUPVALUE()` |
| :--- | :--- | :--- |
| **Model Relationship Required?** | ✅ **Strictly Required:** Must have an active 1-to-Many physical relationship. | ❌ **Not Required:** Operates on disconnected tables or arbitrary search conditions. |
| **Search Mechanism** | Direct VertiPaq pointer navigation along relationship. | Exact value scanning across column vectors (similar to SQL join / Excel XLOOKUP). |
| **Multiple Search Conditions** | Single relationship path only. | Supports multiple pairs: `SearchCol1, Val1, SearchCol2, Val2...` |
| **Duplicate Match Handling** | Guaranteed 1 match by 1-side primary key uniqueness. | ⚠️ **Throws Error** if multiple distinct values match the criteria. |

---

## 💡 Production DAX Solutions

### Calculated Column Definition (`FactSales` Table):
```dax
Target Margin Rate = 
LOOKUPVALUE(
    SalesTargets[TargetMarginRate],
    SalesTargets[ProductCategory],
    FactSales[ProductCategory]
)
```

### Production Pattern with Fallback (Handling Missing Targets):
```dax
Target Margin Rate Safe = 
LOOKUPVALUE(
    SalesTargets[TargetMarginRate],
    SalesTargets[ProductCategory],
    FactSales[ProductCategory],
    0.10 -- Default fallback rate if category target is not configured
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **When to Choose `LOOKUPVALUE` over `RELATED`:**
   - Use `RELATED` when an active relationship exists; it is significantly faster because VertiPaq uses pre-indexed relationship bitmaps.
   - Use `LOOKUPVALUE` when querying disconnected tables, dealing with multiple search criteria, or referencing tables where creating an active relationship is restricted by model schema rules.

2. **The "Multiple Matches" Interview Trap:**
   - If `SalesTargets` contains duplicate rows for "Electronics" with different rates (e.g. 0.25 and 0.30), `LOOKUPVALUE` throws a runtime error: *"A table of multiple values was supplied where a single value was expected."*
   - Defense: Ensure search keys are unique in the target table, or provide additional search criteria columns to identify a single unique row.
