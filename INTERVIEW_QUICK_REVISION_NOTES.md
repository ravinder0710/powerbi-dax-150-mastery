# 🎯 DAX Interview Master Revision Guide (Top 150 Mastery)
> **Target Role:** Data Analyst & BI Developer (0–2 Years Experience)  
> **Purpose:** Deep-dive theoretical understanding, engine internals (VertiPaq), real-world business problems, aur exact technical interview delivery script.

---

# 🟢 PILLAR 1: CORE DAX AGGREGATIONS & MATH

---

## 📌 Q001: Row-by-Row Iterator (`SUMX`) vs Simple Aggregation (`SUM`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Data warehouse me transactional table (`FactSales`) me `Quantity`, `UnitPrice`, aur `DiscountAmount` diya hota hai, lekin direct net revenue (`SalesAmount`) ka column store nahi hota.
* **Why Companies Do This:** Database storage aur cloud data warehouse costs (Snowflake, BigQuery, SQL Server) ko bachane ke liye pre-calculated redundant columns avoid kiye jaate hain.
* **Business Need:** Client ko har order aur overall store ka Net Gross Revenue calculate karna hota hai:
  $$\text{Total Net Sales} = (\text{Quantity} \times \text{UnitPrice}) - \text{DiscountAmount}$$

### 💡 Production Formula:
```dax
Total Net Sales = 
SUMX(
    FactSales, 
    (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount]
)
```

### 🧠 Deep Theory & Engine Mechanics:
1. **`SUM` is Scalar Column Aggregator:** `SUM()` sirf ek single physical column accept karta hai: `SUM(Table[Column])`. Ye arithmetic operators (`*`, `-`) across multiple columns evaluate nahi kar sakta.
2. **`SUMX` is an Iterator:** `SUMX(Table, Expression)` do arguments leta hai:
   - Argument 1: Target Table (`FactSales`).
   - Argument 2: Expression jo har row par evaluate hogi.
3. **Row Context Execution:** `SUMX` table ke upar ek **Row Context** banata hai. Ye row number 1 par jaata hai, `Qty * Price - Discount` compute karta hai, fir row 2 par, aur end me active **Filter Context** ke mutabiq un saari values ko sum kar deta hai.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, `SUM` ek single column aggregate function hai jo row-level arithmetic expressions evaluate nahi kar sakta. Yahan hume do columns ko multiply karke teesra column minus karna hai, isliye humne `SUMX` iterator use kiya jo `FactSales` table par Row Context initiate karke row-by-row net amount compute karta hai aur fir sum deta hai."*

### 🚨 Common Interview Traps:
* **Trap:** *"Calculated column kyu nahi banaya `FactSales` me?"*
* **Defense:** *"Sir, Calculated Column data refresh ke time compute hota hai aur VertiPaq engine ki memory (RAM) aur disk space me permanently save ho jata hai, jisse model heavy ho jata hai. `SUMX` measure query execution ke time memory me dynamically compute hota hai aur RAM save karta hai."*

---

## 📌 Q002: Measure Branching (`Total Cost` & `Total Profit`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Company ko product-level ya store-level par **COGS (Cost of Goods Sold)** aur **Gross Profit** analyze karna hai taaki pata chale ki discount dene ke baad actual profitability kitni bachi.
* **Business Need:** Sales aur Cost ke calculations ko combine karke dynamic Profit nikalna:
  $$\text{Total Profit} = \text{Total Net Sales} - \text{Total Cost}$$

### 💡 Production Formulas:
```dax
Total Cost = 
SUMX(
    FactSales, 
    FactSales[Quantity] * FactSales[UnitCost]
)

Total Profit = 
[Total Net Sales] - [Total Cost]
```

### 🧠 Deep Theory & Engine Mechanics:
1. **DRY Principle (Don't Repeat Yourself):** Agar hum Total Profit nikalte waqt pura formula dobara likhte: `SUMX(FactSales, Qty*Price - Discount) - SUMX(FactSales, Qty*Cost)`, to code monolithic aur unmaintainable ban jata.
2. **Cascading Updates:** Agar kal ko company ka discount rule badalta hai, to hume sirf base measure `[Total Net Sales]` update karna padega. `[Total Profit]` aur `[Profit Margin %]` automatically theek ho jayenge.
3. **Formula Engine (FE) Cache:** Power BI ka Formula Engine reusable measures ke intermediate results ko cache kar leta hai, jisse visuals jaldi render hote hain.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, hum enterprise dashboards me Measure Branching follow karte hain. Hum foundational measures (`[Total Net Sales]` aur `[Total Cost]`) ko reuse karke downstream metrics (`[Total Profit]`) banate hain. Isse code modular, maintainable, aur easy-to-debug rehta hai aur engine cache optimize hoti hai."*

### 🚨 Common Interview Traps:
* **Trap:** *"Measure name ke sath table ka naam kyu nahi likha (e.g. `FactSales[Total Profit]`)?"*
* **Defense:** *"DAX best practice hai ki **Columns** ko hamesha table name ke sath likhte hain (`FactSales[UnitCost]`), aur **Measures** ko hamesha standalone square brackets me likhte hain (`[Total Profit]`), taaki code padhte hi clear ho jaye ki physical column hai ya dynamic measure."*

---

## 📌 Q003: Safe Division (`DIVIDE` vs `/`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Cancelled orders, returns, ya zero-dollar promotions me `[Total Net Sales] = 0` ho jati hai.
* **Problem:** Agar direct `/` (slash) lagayein, to mathematical rule $x / 0$ ki wajah se visual crash ho jata hai aur `Infinity` ya `NaN` error display hota hai.

### 💡 Production Formula:
```dax
Profit Margin % = 
DIVIDE(
    [Total Profit], 
    [Total Net Sales], 
    0
)
```

### 🧠 Deep Theory & Engine Mechanics:
1. **Internal Try-Catch Check:** `DIVIDE()` internally safe-division mechanism perform karta hai. Wo denominator ko zero ya blank check karta hai.
2. **Default Alternate Result:** Agar third argument omit kar dein: `DIVIDE([Total Profit], [Total Net Sales])`, to ye default **`BLANK()`** return karta hai.
3. **Blank Suppression Engine Optimization:** Power BI visuals ki internal property hoti hai ki wo Blank values wali rows ko table/matrix me se **automatically hide** kar dete hain. Agar aap `0` doge, to visual ko lakho empty rows render karni padengi jo report ko slow bana deta hai.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, standard `/` operator division-by-zero par runtime error throw karta hai (`Infinity`/`NaN`), jisse visual toot jata hai. `DIVIDE()` internally denominator validate karta hai aur safely `0` ya `BLANK()` return karta hai. In enterprise reports, `BLANK()` return karwana better hota hai taaki empty rows visual se auto-suppress ho sakein."*

---

## 📌 Q004: Distinct Counting (`DISTINCTCOUNT` vs `COUNTROWS` vs `COUNT`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** E-commerce (Amazon, Flipkart) transactions me ek customer ek order me 3 alag items add karta hai. Data warehouse me is order ki 3 alag rows banti hain (same `OrderID` ke sath).
* **Problem:** Agar management ko dekhna hai ki *"Kitne Orders place hue?"*, to direct rows count karne par data 3x inflated dikhega.

### 💡 Production Formula:
```dax
Total Orders Placed = 
DISTINCTCOUNT(FactSales[OrderID])
```

### 🧠 Deep Theory & Engine Mechanics:
1. **`DISTINCTCOUNT(Column)`:** Column ki unique non-repeating values nikalta hai.
2. **`DISTINCTCOUNTNOBLANK(Column)`:** Agar column me `BLANK()` row ho, to `DISTINCTCOUNT` usko bhi ek distinct value (+1) count kar leta hai. Blank ko ignore karne ke liye `DISTINCTCOUNTNOBLANK` use hota hai.
3. **`COUNTROWS(Table)` vs `COUNT(Column)`:** 
   - `COUNT(Col)` us column ke har cell ko scan karta hai check karne ke liye ki null to nahi hai.
   - `COUNTROWS(Table)` VertiPaq engine ki internal **Table Metadata** se direct total rows read kar leta hai bina kisi data page ko scan kiye. Isliye `COUNTROWS` 100x fast hota hai!

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, multi-item transaction tables me OrderID repeat hoti hai. Actual customer checkouts nikalne ke liye `DISTINCTCOUNT` zaroori hai. Rows count karne ke liye hamesha `COUNTROWS(Table)` use karna chahiye na ki `COUNT(Column)`, kyunki `COUNTROWS` storage engine metadata se direct read hota hai."*

---

## 📌 Q005: Average Order Value (AOV) & Basket Size (UPT)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Retail leadership ko customer purchase behavior samajhna hai:
  1. Customer ek visit me average kitne rupaye kharch karta hai (**AOV**).
  2. Customer ek transaction me average kitni items khareedta hai (**UPT / Basket Size**).

### 💡 Production Formulas:
```dax
Average Order Value = 
DIVIDE(
    [Total Net Sales], 
    [Total Orders Placed], 
    0
)

Units Per Transaction = 
DIVIDE(
    SUM(FactSales[Quantity]), 
    [Total Orders Placed], 
    0
)
```

### 🧠 Deep Theory: Why Direct `AVERAGE()` Fails:
1. **Grammar Limitation:** DAX me `AVERAGE([Measure])` syntax error hota hai. `AVERAGE()` function sirf physical column accept karta hai (`AVERAGE(Table[Column])`).
2. **Granularity (Grain) Mismatch:** Agar table me `SalesAmount` ka column hota bhi aur hum `AVERAGE(FactSales[SalesAmount])` lagate, to wo **Average Line-Item Value** nikalta, **Average Order Value nahi**!
   - *Example:* Order 1 me Mouse (₹ 900) aur Battery (₹ 50) hai. `AVERAGE()` in do rows ka average **₹ 475** nikaal dega, jabki us order ki actual value **₹ 950** thi!

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, direct `AVERAGE()` table ki granularity (line-item level) par average karta hai, jabki business ko transaction order-level par average chahiye. Isliye mathematically hume `Total Net Sales` ko `Total Orders Placed` se divide karna padta hai."*

---

## 📌 Q006: Filter Modification using `CALCULATE()`

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Dashboard ke high-level executive cards par specific business categories ka revenue freeze karna hota hai (e.g. *Electronics Sales*, *Fashion Sales*), chahe user page par koi bhi visual dekhe.

### 💡 Production Formula:
```dax
Electronics Net Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics"
)
```

### 🧠 Deep Theory: Why `CALCULATE()` is the God Function:
1. **Only Filter Modifier:** Pooray DAX me `CALCULATE()` akela aisa function hai jo mojooda Filter Context ko alter/modify/overwrite/delete kar sakta hai.
2. **Execution Order:**
   - Step 1: Filter condition (`DimProduct[Category] = "Electronics"`) pehle evaluate hoti hai.
   - Step 2: Outer filter context modify hota hai.
   - Step 3: Expression (`[Total Net Sales]`) evaluate hoti hai naye context me.
3. **Internal Rewrite:** VertiPaq engine background me boolean predicate ko is table expression me convert karta hai:
   ```dax
   FILTER(ALL(DimProduct[Category]), DimProduct[Category] = "Electronics")
   ```

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, `CALCULATE` DAX ka sabse powerful function hai kyunki ye Filter Context ko manipulate kar sakta hai aur Context Transition perform karta hai. Background me ye boolean filter ko `FILTER(ALL(Column), ...)` me rewrite karta hai."*

---

## 📌 Q007: Multiple Filters in `CALCULATE` (Comma `,` vs `&&`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Regional Director ko specific category aur specific store region ka combined performance dekhna hai (`Category = 'Electronics'` AND `Region = 'North'`).

### 💡 Production Formula:
```dax
North Electronics Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics", 
    DimStore[Region] = "North"
)
```

### 🧠 Deep Theory: Comma `,` vs `&&` Internals:
1. **Storage Engine (SE) Bitmaps:** Comma `,` use karne par dono conditions alag-alag arguments bante hain. VertiPaq Storage Engine dono dimension tables ko parallel scan karta hai aur bitmap indexes ka direct intersection leta hai (fastest execution).
2. **Formula Engine (FE) Penalty:** Agar do alag-alag tables ke columns par `&&` lagaya jaye, to Formula Engine ko beech me aana padta hai jo data ko memory me materialize karta hai, jisse query heavy datasets par slow ho sakti hai.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, enterprise level par multi-table AND conditions ke liye hamesha Comma `,` best practice hai. Comma separated filters Storage Engine ko parallel bitmap intersection run karne dete hain, jabki `&&` across tables Formula Engine materialization trigger kar sakta hai."*

---

## 📌 Q008: Logical OR & The IN Operator (`IN` vs `||`)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Marketing team ne bundled campaign launch kiya: 'Electronics' aur 'Home Decor' ka combined revenue track karna hai.

### 💡 Production Formula:
```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] IN { "Electronics", "Home Decor" }
)
```

### 🧠 Deep Theory: SQL `()` vs DAX `{}`:
1. **The Table Constructor `{}`:** DAX me curly braces `{ ... }` ek anonymous 1-column temporary in-memory table banate hain.
2. **Semi-Join Execution:** `IN` operator us physical column aur is temporary table ke beech optimized **Semi-Join** perform karta hai.
3. **Why not `||` (OR):** Chained `||` sequential row checks karta hai, jabki `IN` ko Storage Engine direct hash match filter me execute karta hai.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, DAX me `{}` Table Constructor hota hai jo in-memory list table create karta hai. `IN` operator semi-join execute karta hai jo chained `||` ke muqable clean aur performant hota hai."*

---

## 📌 Q009: Removing Filters using `ALL()` (Grand Total)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Table visual me har category ke samne Company ki overall Grand Total Sales benchmark ke roop me dikhani hai.

### 💡 Production Formula:
```dax
All Products Sales = 
CALCULATE(
    [Total Net Sales], 
    ALL(DimProduct)
)
```

### 🧠 Deep Theory: `ALL(Column)` vs `ALL(Table)`:
1. **`ALL(Column)`:** Sirf us particular column ka filter hatata hai. Agar report page par kisi doosre column (e.g. `Color = 'Red'`) ka slicer hai, to wo measure ko filter karega!
2. **`ALL(Table)`:** Dimension table ke **saare columns** ke filters clear kar deta hai. Chahe user kisi bhi attribute par slicer lagaye, absolute company grand total aayega.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, `ALL()` function `CALCULATE` ke andar filter removal modifier ka kaam karta hai. Agar specific column level share nikalna hai to `ALL(Column)` use karte hain, aur agar absolute immutable grand total chahiye to `ALL(Table)` use karte hain."*

---

## 📌 Q010: Percentage of Total (Contribution Ratio)

### 🏢 Real Business Problem & Scenario:
* **Scenario:** Portfolio contribution analysis (Pareto 80/20 Rule) jahan dekhna hai ki kaun si category business ka sabse bada revenue share generate karti hai.

### 💡 Production Formula:
```dax
% of Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE([Total Net Sales], ALL(DimProduct)),
    0
)
```

### 🧠 Deep Theory: Numerator vs Denominator Filter Context:
* **Numerator:** Visual row ke active Filter Context par evaluate hota hai (`Category = 'Electronics'` -> ₹ 6,300).
* **Denominator:** `CALCULATE(..., ALL())` filter context ko strip karke Grand Total nikalta hai (₹ 7,130).
* Result: $6300 / 7130 = \mathbf{88.36\%}$.

### 🗣️ Interview me Exact kya bolna hai:
> *"Sir, contribution ratio me numerator current row ke incoming filter context par evaluate hota hai, aur denominator me hum `CALCULATE` aur `ALL` use karke filter remove karte hain taaki overall grand total mile, wrapped in `DIVIDE` for safe math."*
