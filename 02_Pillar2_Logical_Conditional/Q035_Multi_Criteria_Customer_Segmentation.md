# Q035: Multi-Criteria Customer Segmentation (`SWITCH(TRUE())` with `&&`)

## 📌 Metadata
- **Pillar:** Pillar 2: Logical, Text & Conditional DAX
- **Question Number:** 035 / 150
- **Difficulty:** Intermediate
- **Target Role:** Data Analyst / BI Developer (0–2 Years)

---

## 🏢 Business Scenario
CRM marketing and retention teams require dynamic customer segmentation combining both monetary value (Spend) and engagement frequency (Orders):
- **Platinum VIP:** Spend $\ge ₹ 50,000$ AND Orders $\ge 10$ (`"💎 Platinum VIP"`)
- **Gold Member:** Spend $\ge ₹ 20,000$ AND Orders $\ge 5$ (`"🥇 Gold Member"`)
- **One-Time Buyer:** Orders $= 1$ (`"🌱 One-Time Buyer"`)
- **Regular Customer:** All other active customers (`"🥉 Regular Customer"`)
- **Prospect (Zero Orders):** Orders is Blank (`"⚪ Prospect"`)

An interviewer will ask:
> *"1. How do you evaluate compound Boolean conditions (`&&` AND, `||` OR) inside a `SWITCH(TRUE())` expression?*
> *2. Why is caching multiple measures inside double-underscore variables (`__VAR`) enterprise best practice?*
> *3. Can this measure be used as a Slicer field? If not, what architecture is required?"*

---

## 📦 Available Measures in Model
- `[Total Net Sales]` $\rightarrow$ Cumulative customer spend
- `[Total Orders]` $\rightarrow$ Distinct order count (`DISTINCTCOUNT(FactSales[OrderID])`)

---

## 📊 Sample Dataset

| CustomerName | Total Net Sales | Total Orders | Expected Tier Badge | Rule Triggered |
| :--- | :---: | :---: | :--- | :--- |
| **Rajesh Sharma** | ₹ 65,000 | 12 | `"💎 Platinum VIP"` | Spend $\ge 50k$ & Orders $\ge 10$ |
| **Pooja Verma** | ₹ 28,000 | 6 | `"🥇 Gold Member"` | Spend $\ge 20k$ & Orders $\ge 5$ |
| **Arun Patel** | ₹ 8,000 | 1 | `"🌱 One-Time Buyer"` | Orders $== 1$ |
| **Karan Gupta** | ₹ 15,000 | 3 | `"🥉 Regular Customer"` | Else fallback |
| **Sunita Rao** | ₹ 0 | *(BLANK)* | `"⚪ Prospect"` | Blank check |

---

## 💡 Production DAX Solutions

### Enterprise Senior Pattern with Double Underscore Variables:
```dax
Customer Tier Segment = 
VAR __SalesVal  = [Total Net Sales]
VAR __OrdersVal = [Total Orders]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(__OrdersVal), "⚪ Prospect",
    __SalesVal >= 50000 && __OrdersVal >= 10, "💎 Platinum VIP",
    __SalesVal >= 20000 && __OrdersVal >= 5,  "🥇 Gold Member",
    __OrdersVal == 1, "🌱 One-Time Buyer",
    "🥉 Regular Customer"
)
```

---

## 🧠 Interview Defense & Technical Takeaways

1. **Why Double-Underscore Variables (`__VarName`):**
   - In production DAX, using `__` prefixes prevents namespace collisions with native column names or table names, visually differentiating local variables from data model entities.
   - Caching `__SalesVal` and `__OrdersVal` ensures both measures evaluate exactly once per customer row, eliminating redundant VertiPaq engine queries.

2. **The "Slicer Limitation" Trap (Architecture Question):**
   - **Interviewer Trap:** *"Can end users use this `[Customer Tier Segment]` measure in a Slicer dropdown to filter the page?"*
   - **Senior Answer:** *"No, sir. Measures cannot slice data. If end users require filtering by Customer Tier, this logic must be implemented as a **Calculated Column** in `DimCustomer` (or upstream in SQL/Power Query) so the discrete tier tags are physically stored in the model."*
