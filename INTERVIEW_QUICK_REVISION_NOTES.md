# 🎯 Power BI & DAX Interview Master Guide (Top 150)
> **Role:** Data Analyst / Power BI Developer (1–2 Years Experience)  
> **Format:** Clean Professional Hinglish — Real Business Scenario, Core Theory, and Exact Interviewer Traps with Verbal IT Delivery Scripts.

---

# 🟢 PILLAR 1: CORE DAX AGGREGATIONS & MATH

---

## 📌 Q001: Row-by-Row Calculation (`SUMX` vs `SUM`)

### 💡 Production DAX:
```dax
Total Net Sales = 
SUMX(
    FactSales, 
    (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount]
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Hamare data warehouse me `FactSales` table ke andar `SalesAmount` ka column nahi hai, sirf `Quantity`, `UnitPrice`, aur `DiscountAmount` diya hai. Client ko total net sales dekhni hai, kaise karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, data warehouse me storage aur ETL refresh time optimize karne ke liye companies pre-calculated columns avoid karti hain. Hum `SUMX` iterator function use karenge jo `FactSales` table par row-by-row ja kar `(Quantity * Price) - Discount` compute karega aur report ke filter context ke hisab se exact Total Net Sales de dega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* `SUM()` ek **Scalar Column Aggregator** hai jo sirf ek single physical column leta hai (`SUM(Table[Col])`). Ye do ya teen columns ke beech mathematical calculation nahi kar sakta.
* `SUMX()` ek **Iterator** hai. Ye table ke upar **Row Context** banata hai, row-by-row expression evaluate karta hai, aur end me un values ka sum karta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Calculated Column kyu nahi banaya?**
  * *Interviewer:* *"Fact table me ja kar calculated column bana lete `Sales = Qty*Price - Discount`, fir simple `SUM` kar lete, SUMX kyu likha?"*
  * *Aapka Jawab:* *"Sir, calculated column data refresh ke waqt RAM aur disk space permanently occupy kar leta hai VertiPaq engine ke andar. Production fact table me lakho rows hoti hain, wahan column add karne se model ka size badh jayega. `SUMX` measure on-demand RAM me evaluate hota hai aur model ko lightweight rakhta hai."*
* **Trap 2: Column naming standard:**
  * *Interviewer:* *"Columns ke aage table name likhna kyu zaroori hai?"*
  * *Aapka Jawab:* *"DAX ka enterprise standard hai ki physical column ko hamesha `TableName[ColumnName]` likhte hain aur dynamic measure ko standalone `[MeasureName]` likhte hain, taaki code audit aur debugging me confusion na ho."*

---

## 📌 Q002: Measure Branching (`Total Cost` & `Total Profit`)

### 💡 Production DAX:
```dax
Total Cost = 
SUMX(FactSales, FactSales[Quantity] * FactSales[UnitCost])

Total Profit = 
[Total Net Sales] - [Total Cost]
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Company ko COGS (Cost of Goods Sold) aur actual Gross Profit track karna hai. Pichla measure `[Total Net Sales]` already bana hua hai. Profit ka measure kaise design karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum **Measure Branching** technique follow karenge. Pehle `[Total Cost]` calculate karenge, fir direct `[Total Net Sales] - [Total Cost]` likh kar `[Total Profit]` nikalenge bina poora sales ka formula repeat kiye."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* **Measure Branching** ka matlab hota hai pehle se bane hue chhote measures ko aage ke complex formulas me reuse karna.
* Ye software engineering ke **DRY Principle (Don't Repeat Yourself)** par kaam karta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Ek hi lamba measure kyu nahi banaya?**
  * *Interviewer:* *"Direct `Profit = SUMX(Sales) - SUMX(Cost)` ek hi formula me kyu nahi likha? Alag-alag measures kyu banaye?"*
  * *Aapka Jawab:* *"Sir, agar kal ko discount ya tax calculation ka business rule badalta hai, to mujhe sirf base measure `[Total Net Sales]` update karna hoga. `[Total Profit]` aur aage aane wala `[Profit Margin %]` automatically theek ho jayenge. Agar ek hi lamba formula likhte to sab jagah manually change karna padta."*
* **Trap 2: Performance par kya asar padta hai?**
  * *Interviewer:* *"Kya measure reuse karne se query slow hoti hai?"*
  * *Aapka Jawab:* *"Nahi sir, Power BI ka Formula Engine reusable base measures ke results ko query execution ke time cache kar leta hai, isliye visual load time par koi negative impact nahi padta."*

---

## 📌 Q003: Safe Division (`DIVIDE` vs `/`)

### 💡 Production DAX:
```dax
Profit Margin % = 
DIVIDE([Total Profit], [Total Net Sales], 0)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Client ko Profit Margin % dekhna hai. Data me kuch cancelled orders aur zero-sale returns hain jahan Total Sales = 0 hai. Division-by-zero error kaise handle karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, regular `/` (slash) lagane par division-by-zero hone par `Infinity` ya `NaN` error aayega aur visual crash ho jayega. Hum `DIVIDE()` function use karenge jo internally zero validate karke report ko tootne se bachata hai."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* `DIVIDE(Numerator, Denominator, [AlternateResult])` internally safe division karta hai.
* Agar 3rd parameter omit kar diya jaye, to ye default **`BLANK()`** return karta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: IF condition vs DIVIDE:**
  * *Interviewer:* *"Kya hum `IF([Sales] = 0, BLANK(), [Profit] / [Sales])` use kar sakte hain?"*
  * *Aapka Jawab:* *"Sir, `IF` statement me `[Sales]` do baar evaluate hota hai (ek baar condition check karne me, ek baar divide karne me). `DIVIDE()` internally ek hi baar evaluate karta hai, jo performance wise zyada fast aur clean hota hai."*
* **Trap 2: 0 dena better hai ya BLANK() chhodna?**
  * *Interviewer:* *"3rd parameter me 0 dena chahiye ya blank chhodna chahiye?"*
  * *Aapka Jawab:* *"Sir, reporting me `BLANK()` chhodna better practice hai, kyunki Power BI visuals blank rows ko automatically hide (auto-suppress) kar dete hain jisse report fast render hoti hai. Agar hum `0` denge to visual ko lakho zero-sales wali rows bhi zabardasti draw karni padti hain."*

---

## 📌 Q004: Distinct Counting (`DISTINCTCOUNT` vs `COUNTROWS`)

### 💡 Production DAX:
```dax
Total Orders Placed = 
DISTINCTCOUNT(FactSales[OrderID])
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Hamare transaction table me ek customer ek order me 3 items khareedta hai to 3 rows banti hain (OrderID repeat hoti hai). Client ko KPI card me 'Total Orders Placed' dikhana hai, kaise karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, transactional fact table me ek order multiple line-items me split hota hai. Agar hum direct rows count kar lenge to order count 3x inflated dikhega. Actual unique customer checkouts nikalne ke liye hume `DISTINCTCOUNT(FactSales[OrderID])` use karna hoga."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* `DISTINCTCOUNT(Column)`: Unique values nikalta hai.
* `COUNTROWS(Table)`: Table ki total physical rows count karta hai.
* `COUNT(Column)`: Column ke non-blank cells count karta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Blank OrderID Trap:**
  * *Interviewer:* *"Agar column me Blank value ho, to kya `DISTINCTCOUNT` usko count karega?"*
  * *Aapka Jawab:* *"Haan sir, `DISTINCTCOUNT` blank ko bhi ek distinct value maankar count me +1 kar deta hai. Agar business rule hai ki blank orders count na hon, to hum **`DISTINCTCOUNTNOBLANK()`** function use karte hain."*
* **Trap 2: COUNTROWS vs COUNT Performance:**
  * *Interviewer:* *"Rows count karne ke liye `COUNT(Column)` aur `COUNTROWS(Table)` me se kaun sa fast hai?"*
  * *Aapka Jawab:* *"Sir, `COUNTROWS(Table)` hamesha fast hota hai. `COUNT(Col)` poore column ke cells ko scan karta hai, jabki `COUNTROWS` VertiPaq engine ki internal table metadata se direct row count read kar leta hai bina data page scan kiye."*

---

## 📌 Q005: Average Order Value (AOV) & Basket Size (UPT)

### 💡 Production DAX:
```dax
Average Order Value = 
DIVIDE([Total Net Sales], [Total Orders Placed], 0)

Units Per Transaction = 
DIVIDE(SUM(FactSales[Quantity]), [Total Orders Placed], 0)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Retail business ko dekhna hai ki har order par customer average kitne rupaye kharch karta hai (AOV) aur average kitni items leta hai (UPT). Direct `AVERAGE` function use kyu nahi kar sakte?"*
* **Aapka Professional Jawab:**
  > *"Sir, table ki granularity line-item level par hai. Agar hum direct `AVERAGE` lagayenge to wo Line-Item ka average nikalega, Order ka average nahi. Isliye transaction order-level average nikalne ke liye mathematically Total Sales ko Total Orders se divide karna padega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* **Grain Mismatch:** Fact table me 1 row = 1 item in an order.
* Agar Order-1 me ₹ 900 ka mouse aur ₹ 50 ki battery hai, to `AVERAGE` function in do rows ka average **₹ 475** batayega, jabki customer ne us order me **₹ 950** spend kiye hain.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Measure inside AVERAGE:**
  * *Interviewer:* *"Kya hum `AVERAGE([Total Net Sales])` likh sakte hain?"*
  * *Aapka Jawab:* *"Nahi sir, direct syntax compilation error aayega. DAX me `AVERAGE()` function sirf ek physical column accept karta hai, dynamic measure nahi."*
* **Trap 2: Inactive periods:**
  * *Interviewer:* *"Agar kisi store me kisi din ek bhi order na aaya ho to AOV kya aayega?"*
  * *Aapka Jawab:* *"Humne measure ko `DIVIDE()` me wrap kiya hai, isliye division by zero safely handle ho kar 0 ya Blank return karega aur report crash nahi hogi."*

---

## 📌 Q006: Filter Modification using `CALCULATE()`

### 💡 Production DAX:
```dax
Electronics Net Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics"
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Dashboard ke overview tile par mujhe sirf 'Electronics' category ki sales freeze karke dikhani hai, chahe user slicer me kuch bhi select kare. Kaise karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `CALCULATE()` function use karenge. `CALCULATE` existing visual ke filter context ko modify karke us par `Category = 'Electronics'` ka filter forcefully apply kar dega aur exact category revenue return karega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* `CALCULATE()` akela aisa function hai DAX me jo **Filter Context ko modify/add/overwrite** kar sakta hai.
* Background me engine boolean filter ko is table expression me rewrite karta hai:
  `FILTER(ALL(DimProduct[Category]), DimProduct[Category] = "Electronics")`

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Wrapping Measure in SUM:**
  * *Interviewer:* *"Kya hum `CALCULATE(SUM([Total Net Sales]), ...)` likh sakte hain?"*
  * *Aapka Jawab:* *"Nahi sir! `[Total Net Sales]` already ek calculated measure hai jiske andar aggregation pehle se defined hai. Measure ke upar `SUM()` lagane se compilation error aayega."*
* **Trap 2: Execution Order:**
  * *Interviewer:* *"CALCULATE me pehle expression execute hoti hai ya filter?"*
  * *Aapka Jawab:* *"Pehle filter condition evaluate hoti hai, fir filter context modify hota hai, aur sabse aakhri me pehla parameter (expression) us naye context me execute hota hai."*

---

## 📌 Q007: Multiple Filters in `CALCULATE` (Comma `,` vs `&&`)

### 💡 Production DAX:
```dax
North Electronics Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics", 
    DimStore[Region] = "North"
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Mujhe specific category aur specific store region ka combined revenue dekhna hai (Electronics in North region). Multiple filters kaise apply karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `CALCULATE` ke andar dono filter conditions ko alag-alag arguments ke roop me **Comma `,`** se separate karke pass karenge. Ye dono conditions ka logical AND intersection banata hai."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* Comma separated filters VertiPaq **Storage Engine (SE)** ko dono dimension tables ko parallel scan karne dete hain aur bitmap indexes ka direct intersection lete hain.
* Ye sub-millisecond execution deta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Comma vs && Performance:**
  * *Interviewer:* *"Dono ka output same hai, to Comma kyu best practice hai aur `&&` kyu nahi?"*
  * *Aapka Jawab:* *"Sir, Comma syntax Storage Engine me natively evaluate hota hai. Jabki do alag-alag tables ke columns par `&&` lagane se Formula Engine (FE) ko beech me aana padta hai jo data ko memory me materialize karta hai, jisse heavy datasets par query slow ho sakti hai."*
* **Trap 2: Multi-table predicate error:**
  * *Interviewer:* *"Older DAX me alag-alag tables par `&&` lagane par kya hota tha?"*
  * *Aapka Jawab:* *"Older engines me syntax error aa jata tha. Modern DAX support karta hai lekin best practice Comma hi hai."*

---

## 📌 Q008: Logical OR & The IN Operator (`IN` vs `||`)

### 💡 Production DAX:
```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] IN { "Electronics", "Home Decor" }
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Marketing campaign ke liye mujhe do specific categories ('Electronics' aur 'Home Decor') ka combined sales chahiye. Kaise likhoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `CALCULATE` ke andar `IN` operator use karenge with table constructor `{}`. Ye dono categories ka combined union filter karke net sales calculate kar dega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* DAX me curly braces `{ ... }` ek **Table Constructor** hota hai jo 1-column anonymous temporary in-memory table create karta hai.
* `IN` operator is temporary table aur column ke beech **Semi-Join** perform karta hai.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: SQL vs DAX Syntax:**
  * *Interviewer:* *"Aapne `IN ('A', 'B')` kyu nahi likha jaise SQL me likhte hain? Curly braces `{}` kyu lagaye?"*
  * *Aapka Jawab:* *"Sir, DAX ek table-oriented language hai. SQL me `()` simple value tuple hota hai, lekin DAX me `{}` Table Constructor operator hai jo in-memory table banata hai. DAX me `{}` mandatory hai."*
* **Trap 2: `IN` vs `||` (Logical OR):**
  * *Interviewer:* *"Chained `||` ke muqable `IN` kyu prefer kiya?"*
  * *Aapka Jawab:* *"Sir, agar 5-10 categories hon to `||` likhne par code unreadable aur slow ho jata hai. `IN` operator ko Storage Engine direct hash match filter me execute karta hai jo lakho rows par bohot fast hota hai."*

---

## 📌 Q009: Removing Filters using `ALL()` (Grand Total)

### 💡 Production DAX:
```dax
All Products Sales = 
CALCULATE(
    [Total Net Sales], 
    ALL(DimProduct)
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Table visual me har Category row ke samne mujhe Company ka Grand Total Sales benchmark ke roop me dikhana hai. Visual ka filter kaise hataoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, standard visual me har row apni category se filter hoti hai. Hum `CALCULATE` ke andar `ALL(DimProduct)` lagayenge jo active category filter ko clear/remove kar dega aur hamesha universal Grand Total return karega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* Inside `CALCULATE()`, `ALL()` ek **Filter Removal Modifier** ka kaam karta hai.
* Ye VertiPaq engine ko order deta hai ki is table/column par aane wale incoming slicer/row filters ko ignore karo.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: ALL(Column) vs ALL(Table):**
  * *Interviewer:* *"Agar page par `SubCategory = 'Laptops'` ka slicer ho, to kya `ALL(DimProduct[Category])` par asar padega?"*
  * *Aapka Jawab:* *"Haan sir! Kyunki humne sirf Category column ka filter hataya hai, SubCategory ka slicer abhi bhi measure ko filter karega. Agar absolute immutable grand total chahiye to hume poori table par `ALL(DimProduct)` lagana hoga."*
* **Trap 2: Table Function vs Modifier:**
  * *Interviewer:* *"Kya `ALL()` ek table function hai ya filter modifier?"*
  * *Aapka Jawab:* *"Both sir! `CALCULATE` ke andar ye Filter Modifier hai, aur `CALCULATE` ke bahar ye Table Function hai jo distinct unique rows return karta hai."*

---

## 📌 Q010: Percentage of Total (Contribution Ratio)

### 💡 Production DAX:
```dax
-- Standalone Standard Format:
% of Total Sales = 
DIVIDE(
    [Total Net Sales], 
    CALCULATE([Total Net Sales], ALL(DimProduct)), 
    0
)
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Management ko Portfolio Contribution / Pareto 80-20 analyze karna hai taaki pata chale kaun si category sabse zyada revenue generate karti hai. `% of Total` ka measure kaise banaoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `DIVIDE` function use karenge jisme Numerator me current category sales (`[Total Net Sales]`) aayega aur Denominator me `CALCULATE([Total Net Sales], ALL(DimProduct))` aayega jo filter hata kar company grand total dega."*

### 🧠 Deep Concept (Jo dimag me clear hona chahiye):
* **Numerator:** Row ke incoming Filter Context par evaluate hota hai (e.g. Electronics = ₹ 6,300).
* **Denominator:** `ALL()` ke dwara filter context clear karke Grand Total par evaluate hota hai (₹ 7,130).
* Result: $6300 / 7130 = \mathbf{88.36\%}$.

### 🚨 Common Interview Traps (Counter-Questions & IT Answers):
* **Trap 1: Matrix Subtotal vs Grand Total:**
  * *Interviewer:* *"Agar Matrix visual me Category ke andar SubCategory drill-down ho, to kya `ALL(DimProduct)` Category-level subtotal par 100% dega ya Grand Total par?"*
  * *Aapka Jawab:* *"Sir, ye Grand Total par 100% dega. Agar hume parent category ke subtotal level par percentage chahiye ho, to hume `ALLSELECTED()` ya `ALLEXCEPT()` use karna padega."*
* **Trap 2: Direct Single Measure:**
  * *Interviewer:* *"Bina kisi helper measure ke direct ek measure me likho."*
  * *Aapka Jawab:* *"Numerator me direct `[Total Net Sales]` aur denominator me `CALCULATE([Total Net Sales], ALL(DimProduct))` daal kar `DIVIDE` me wrap kar denge."*


---

## 📌 Q011: Row-by-Row Maximum Value (`MAXX` vs `MAX`)

### 💡 Production DAX:
```dax
Max Sale Value = 
MAXX(FactSales, FactSales[Quantity] * FactSales[UnitPrice])
```

### 🏢 Business Scenario (Interviewer kaise puchega & Aapko kya bolna hai):
* **Interviewer:** *"Retail store manager ko single line-item me sabse badi sale nikalni hai. Fact table me direct sales column nahi hai, sirf Quantity aur UnitPrice diye hain. Kaise karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `MAXX` iterator function use karenge jo `FactSales` par Row Context banakar row-by-row `Quantity * UnitPrice` multiply karega aur unme se highest single-item sale return karega."*

### 🧠 Deep Concept:
* `MAX(Table[Col])` sirf ek single column par chalta hai, do columns ke beech multiplication nahi kar sakta.
* `MAXX(Table, Expression)` row-by-row calculation karke maximum value nikalta hai.

### 🚨 Common Interview Traps & Defense:
* **Trap: `MAX()` do arguments kab leta hai?**
  * *Defense:* "`MAX(val1, val2)` do scalar values ko compare karne ke liye 2 arguments leta hai (e.g. `MAX(10, 25)` -> 25). Lekin multi-row expressions ke liye iterator `MAXX` hi lagta hai."

---

## 📌 Q012: Row-by-Row Minimum with Zero Exclusion (`MINX` & `FILTER`)

### 💡 Production DAX:
```dax
Min Paid Sale Value = 
MINX(
    FILTER(FactSales, FactSales[UnitPrice] > 0),
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Hamare data me free samples aur zero-price promotional items hain. Lowest paid sale nikalni hai (> ₹ 0). Direct `MIN()` kyu fail hoga?"*
* **Aapka Professional Jawab:**
  > *"Sir, direct `MIN()` lagane par answer ₹ 0 aayega jo commercial pricing ko misguide karega. Hum `FILTER` table function se pehle ₹ 0 wali rows ko exclude karenge aur fir `MINX` se lowest paid sale nikalenge."*

### 🚨 Common Interview Traps & Defense:
* **Trap: `FILTER` vs `CALCULATE` inside `MINX`:**
  * *Defense:* "`CALCULATE(MINX(FactSales, Qty*Price), FactSales[UnitPrice] > 0)` bhi likh sakte hain jo VertiPaq Storage Engine bitmap filtering use karke fast execute hota hai."

---

## 📌 Q013: Average Daily Sales using `AVERAGEX` & `VALUES`

### 💡 Production DAX:
```dax
Average Daily Sales = 
AVERAGEX(
    VALUES(DimDate[Date]),
    [Total Net Sales]
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"February me 28 din hote hain aur March me 31 din. True month-over-month run-rate compare karne ke liye Average Daily Sales kaise nikaloge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum `AVERAGEX` iterator use karenge jisme pehla parameter `VALUES(DimDate[Date])` aayega jo visible calendar dates dega, aur doosra parameter `[Total Net Sales]` aayega jo har date par Context Transition ke through daily sales evaluate karega aur unka average dega."*

### 🧠 Deep Concept (Context Transition):
* `[Total Net Sales]` ek measure hai jiske andar implicit `CALCULATE()` hota hai.
* Jab `AVERAGEX` dates par iterate karta hai, to date ka Row Context **Filter Context** me convert ho jata hai (Context Transition).

### 🚨 Common Interview Traps & Defense:
* **Trap: `VALUES()` vs `ALL()` in AVERAGEX:**
  * *Defense:* "`ALL()` ignore kar dega user ke slicer ko aur saare 365 din se divide kar dega. `VALUES()` current slicer context ki dates ko respect karta hai."

---

## 📌 Q014: Average Selling Price (ASP) vs Average Order Value (AOV)

### 💡 Production DAX:
```dax
Average Selling Price (ASP) = 
DIVIDE([Total Net Sales], SUM(FactSales[Quantity]), 0)

Average Order Value (AOV) = 
DIVIDE([Total Net Sales], [Total Orders Placed], 0)
```

### 🏢 Business Scenario (The Retail Difference):
* **ASP:** Per-unit realization ($\text{Total Sales} / \text{Total Quantity}$).
* **AOV:** Per-basket transaction ($\text{Total Sales} / \text{Total Orders}$).
* **Golden Retail Equation:** $\text{AOV} = \text{ASP} \times \text{UPT}$ (Average Order Value = Average Selling Price $\times$ Units Per Transaction).

### 🚨 Common Interview Traps & Defense:
* **Trap: Why not `AVERAGE(UnitPrice)` for ASP?**
  * *Defense:* "`AVERAGE(UnitPrice)` unweighted average deta hai jo galat hai. True ASP hamesha volume-weighted hota hai (`Total Sales / Total Quantity`)."

---

## 📌 Q015: Realized Discount Percentage (`Total Discount / Gross Sales`)

### 💡 Production DAX:
```dax
Total Gross Sales = SUMX(FactSales, FactSales[Quantity] * FactSales[UnitPrice])
Total Discount Amount = SUM(FactSales[DiscountAmount])

Realized Discount % = 
DIVIDE([Total Discount Amount], [Total Gross Sales], 0)
```

### 🏢 Business Scenario (Gross vs Net Sales):
* **Gross Sales:** Original sticker price / MRP billing ($\text{Qty} \times \text{Price}$).
* **Net Sales:** Actual bank revenue after discounts ($\text{Gross} - \text{Discount}$). Company profit hamesha Net Sales par evaluate hota hai.

### 🚨 Common Interview Traps & Defense:
* **Trap: Net Sales se divide kyu nahi kiya?**
  * *Defense:* "Commercial discounts hamesha original sticker price (Gross MRP) par offer hote hain, isliye base hamesha Gross Sales hota hai."

---

## 📌 Q016: Safe Null Handling & Fallback Display (`COALESCE` vs `ISBLANK`)

### 💡 Production DAX:
```dax
Sales Display Value = 
COALESCE([Total Net Sales], 0)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Naye products jinki koi sale nahi hui wahan visual me Blank dikh raha hai. Client ko 0 chahiye, kaise karoge?"*
* **Aapka Professional Jawab:**
  > *"Sir, hum modern DAX ka `COALESCE()` function use karenge. Ye pehli non-blank value return karta hai, aur blank hone par fallback 0 de deta hai bina duplicate measure evaluation ke."*

### 🚨 Common Interview Traps & Defense:
* **Trap: `COALESCE` vs `+ 0` trick:**
  * *Defense:* "`+ 0` lagana auto-blank suppression ko destroy kar deta hai jisse hazaron empty rows visual me draw hone lagti hain aur dashboard slow ho jata hai."

---

## 📌 Q017: Counting Missing Records (`COUNTBLANK` vs `COUNTROWS(FILTER)`)

### 💡 Production DAX:
```dax
Incomplete Profiles = 
COUNTBLANK(DimCustomer[City])
```

### 🏢 Business Scenario:
* **Interviewer:** *"Data Governance audit me missing customer profiles ginni hain jahan City blank hai."*
* **Aapka Professional Jawab:**
  > *"Sir, single column ke blanks count karne ke liye `COUNTBLANK(Table[Column])` direct aur fastest function hai. Multi-column checks ke liye hum `COUNTROWS(FILTER(Table, ISBLANK(Col1) || ISBLANK(Col2)))` use karte hain."*

---

## 📌 Q018: Strict Equality Operator (`==` vs `=`)

### 💡 Production DAX:
```dax
Zero Discount Products = 
COUNTROWS(
    FILTER(
        DimProduct,
        DimProduct[DiscountGiven] == 0
    )
)
```

### 🧠 Deep Theory: Blank vs Zero:
* In DAX: `0 = BLANK()` evaluates to **TRUE**!
* In DAX: `0 == BLANK()` evaluates strictly to **FALSE**!
* Double `==` strict value aur data type matching enforce karta hai.

---

## 📌 Q019: Safe Omnichannel Mathematical Addition (`COALESCE` & Variables)

### 💡 Production DAX:
```dax
Total Omnichannel Sales = 
COALESCE([Store Net Sales], 0) + COALESCE([Online Net Sales], 0)
```

### 🧠 Senior Pattern (Preserving Blank Suppression with Variables):
```dax
Total Omnichannel Sales = 
VAR StoreSales = [Store Net Sales]
VAR OnlineSales = [Online Net Sales]
RETURN
IF(
    ISBLANK(StoreSales) && ISBLANK(OnlineSales),
    BLANK(),
    COALESCE(StoreSales, 0) + COALESCE(OnlineSales, 0)
)
```


---

## 📌 Q020: Calculated Column vs Measure (Slicer Bucketing & Categorization)

### 💡 Production DAX (Calculated Column in `DimProduct`):
```dax
Price Category = 
SWITCH(
    TRUE(),
    DimProduct[UnitPrice] < 20, "Budget",
    DimProduct[UnitPrice] <= 100, "Mid-Range",
    DimProduct[UnitPrice] > 100, "Premium",
    "Unknown"
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Products ko price ke hisaab se categorize karke ek Slicer/Filter banana hai. Aap Calculated Column banaoge ya Measure? Kyu?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, Slicers, Matrix Rows/Columns, aur Chart Axes ke liye distinct dimensional values chahiye hoti hain jo data model me physically exist karein. Measure dynamically scalar aggregate return karta hai aur visual canvas par filter options generate nahi kar sakta, isliye Slicing aur Bucketing ke liye **Calculated Column mandatory** hota hai."*

### 🚨 Common Interview Traps:
* **Trap 1: Trying to put Measure in a Slicer:** Power BI me Measure ko Slicer field me drag & drop karna allowed hi nahi hai.
* **Trap 2: Storage vs Performance:** Calculated Column data refresh ke waqt calculate hokar VertiPaq RAM me space leta hai (Row Context), jabki Measure query-time par compute hota hai (Filter Context). Static buckets agar possible ho toh SQL View ya Power Query me banana best practice hai, par DAX me banana ho toh Calculated Column use hoga.


---

## 📌 Q021: Active Customer Penetration % (Conversion Ratio)

### 💡 Production DAX:
```dax
Active Customer % = 
DIVIDE(
    DISTINCTCOUNT(FactSales[CustomerKey]),
    COUNTROWS(DimCustomer),
    0
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Registered customer base me se kitne percent logon ne actual purchase kiya hai, iska KPI measure likho."*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, Numerator me `FactSales[CustomerKey]` par `DISTINCTCOUNT` chalega kyunki ek customer ke multiple orders ho sakte hain. Denominator me master table `DimCustomer` par `COUNTROWS` chalega. Dono ko safe division ke liye `DIVIDE` me wrap karenge taaki zero division error na aaye."*

### 🚨 Common Interview Traps:
* **Trap 1: `COUNT` vs `DISTINCTCOUNT` on Fact Table:** Agar FactSales par normal `COUNT` laga diya toh wo order rows count kar lega (repeat orders ko alag customer samajh lega).
* **Trap 2: Context Filtering vs Global Benchmark:** Slicer lagane par dono numerator aur denominator filter honge. Agar interviewer bole ki "Denominator hamesha total registered customers hi rehna chahiye irrespective of slicer", toh Denominator me `COUNTROWS(ALL(DimCustomer))` lagana hoga.


---

## 📌 Q022: Multi-Table Iterator & The `RELATED` Function

### 💡 Production DAX:
```dax
Total Cost = 
SUMX(
    FactSales,
    FactSales[Quantity] * RELATED(DimProduct[StandardCost])
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Fact table me Quantity hai, lekin Cost Price DimProduct me hai. Row-by-row Total Cost kaise nikaloge? Kya direct multiply kar sakte hain?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, direct multiply nahi kar sakte kyunki `SUMX` ka Row Context automatically relationship follow nahi karta. Many side (`FactSales`) se One side (`DimProduct`) ka value pull karne ke liye `RELATED()` function use karna mandatory hota hai."*

### 🚨 Common Interview Traps:
* **Trap 1: `RELATED` vs `RELATEDTABLE`:** 
  - `RELATED(Column)`: Many-to-One traversal karta hai aur single value (scalar) return karta hai.
  - `RELATEDTABLE(Table)`: One-to-Many traversal karta hai aur puri table return karta hai (e.g. `COUNTROWS(RELATEDTABLE(FactSales))`).
* **Trap 2: Active Relationship Requirement:** `RELATED()` sirf tab kaam karta hai jab model me active relationship pehle se bani ho. Agar relationship nahi hai toh `LOOKUPVALUE()` use karna padta hai.


---

## 📌 Q023: Disconnected Table Lookups (`LOOKUPVALUE` vs `RELATED`)

### 💡 Production DAX:
```dax
Target Margin Rate = 
LOOKUPVALUE(
    SalesTargets[TargetMarginRate],
    SalesTargets[ProductCategory],
    FactSales[ProductCategory],
    0.10 -- Optional default fallback
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Agar do tables ke beech physical relationship nahi hai (disconnected table), toh kya `RELATED()` kaam karega? Aur bina relationship ke dusri table se value kaise laoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, `RELATED()` strictly active relationship mangta hai, bina relationship ke wo kaam nahi karega. Disconnected tables ya dynamic criteria ke liye hum `LOOKUPVALUE()` use karte hain (jo Excel ke XLOOKUP jaisa kaam karta hai).*
  > *Syntax: `LOOKUPVALUE(ResultColumn, SearchColumn, SearchValue, [AlternateResult])`."*

### 🚨 Common Interview Traps:
* **Trap 1: Multiple Matches Error:** Agar search table me same category ke do alag-alag rate mil gaye, toh `LOOKUPVALUE` runtime error de dega. Search column me values unique honi chahiye.
* **Trap 2: Performance:** Agar model me already relationship exist karti hai, toh hamesha `RELATED()` prefer karna chahiye kyunki wo VertiPaq pre-indexed pointers use karta hai aur `LOOKUPVALUE` se kaafi fast hota hai.


---

## 📌 Q024: Text Concatenation Across Rows (`CONCATENATEX`)

### 💡 Production DAX:
```dax
Purchased Products List = 
CONCATENATEX(
    VALUES(FactSales[ProductName]),
    FactSales[ProductName],
    ", ",
    FactSales[ProductName],
    ASC
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Customer profile card ya tooltip me khareede gaye sabhi products ke naam ek comma-separated text string me kaise dikhaoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, rows ke text strings ko concatenate karne ke liye DAX ka akela text iterator `CONCATENATEX` use hota hai. Duplicate products avoid karne ke liye hum table parameter me `VALUES(Table[Column])` pass karte hain aur 4th/5th parameter se alphabetical sort kar dete hain."*

### 🚨 Common Interview Traps:
* **Trap 1: `VALUE` vs `VALUES`:** `VALUE()` scalar function hai jo string ko number banata hai, jabki `VALUES()` table function hai jo unique values ki table deta hai.
* **Trap 2: Large String Performance:** Agar filter context broad ho (e.g. bina filter ke puri table par chalana), toh visual memory explode ho sakti hai. Isliye enterprise me hum `HASONEVALUE()` ya `TOPN` guardrails lagate hain.


---

## 📌 Q025: Outlier-Resilient Central Tendency (`MEDIAN` & `MEDIANX`)

### 💡 Production DAX:
```dax
Median Order Value = 
MEDIANX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Jab data me extreme outliers (jaise ek single \$50k ka order) pure average ko distort kar rahe ho, toh typical customer spend dikhane ke liye konsa DAX function use karoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, `AVERAGE` outliers se jaldi skew ho jata hai. Aise scenario me central tendency represent karne ke liye 50th percentile yaani `MEDIAN` use kiya jata hai. Agar row-by-row expression ka median nikalna ho, toh hum iterator `MEDIANX(Table, Expression)` use karte hain."*

### 🚨 Common Interview Traps:
* **Trap 1: Even vs Odd Rows:** DAX ka `MEDIAN` agar even number of rows milein toh middle ke 2 values ka average khud nikal deta hai.
* **Trap 2: Performance:** `AVERAGE` simple sum/count karta hai jabki `MEDIAN` ko data sort karna padta hai 50th percentile dhundne ke liye, isliye multi-million rows par `MEDIAN` slightly zyada CPU time leta hai.


---

## 📌 Q026: Financial Rounding (`ROUND` vs `MROUND`)

### 💡 Production DAX:
```dax
-- Measure 1: 2 Decimal Places
Incentive Rounded (2 Decimals) = 
ROUND(SUM(FactDeliveryPayout[CalculatedIncentive]), 2)

-- Measure 2: Nearest Multiple of 5
Cash Payout (Multiple of 5) = 
MROUND(SUM(FactDeliveryPayout[CalculatedIncentive]), 5)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Financial reports me decimal rounding aur cash payout me nearest multiple rounding (e.g. ₹ 5 ya ₹ 10) kaise karte hain?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, decimal places fix karne ke liye `ROUND(Number, Digits)` use hota hai. Kisi business multiple (jaise 5, 10, 50) ke nearest number par round karne ke liye `MROUND(Number, Multiple)` use hota hai. Always up ke liye `CEILING` aur always down ke liye `FLOOR` use karte hain."*

### 🚨 Common Interview Traps:
* **Trap 1: Negative Digits in `ROUND`:** Agar `ROUND(1250, -2)` likhenge toh result `1300` aayega (hundreds digit par round karega).
* **Trap 2: `MROUND` Sign Mismatch Error:** Agar number negative ho aur multiple positive (jaise `MROUND(-48, 5)`), toh DAX runtime error fek deta hai. Dono ka sign same hona zaroori hai.


---

## 📌 Q027: Sub-Category Contribution within Parent Category (`ALLEXCEPT`)

### 💡 Production DAX:
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

### 🏢 Business Scenario:
* **Interviewer:** *"Matrix visual me har Sub-Category ka percentage pure store ke total se nahi, balki uski parent Category ke total se divide karke nikalna hai. Kaise karoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, `ALL(DimProduct)` pure table ke filters hata kar Grand Total de deta hai. Hume sirf SubCategory ka filter hatana hai aur Category ka filter bacha kar rakhna hai. Iske liye Denominator me `CALCULATE([Total Net Sales], ALLEXCEPT(DimProduct, DimProduct[Category]))` lagayenge."*

### 🚨 Common Interview Traps:
* **Trap 1: Filters on Other Tables:** `ALLEXCEPT` sirf apni specified table (`DimProduct`) ke filters hatata hai. Agar slicer me `DimDate[Year] = 2024` ya `DimStore[Region] = 'North'` laga ho, toh wo filter intact rehta hai, jo ki financial reporting ke liye bilkul sahi behaviour hai.
* **Trap 2: `ALLEXCEPT` vs `REMOVEFILTERS`:** Modern DAX me `REMOVEFILTERS(DimProduct[SubCategory])` bhi use ho sakta hai, par jab table me multiple sub-levels ho toh `ALLEXCEPT` zyada safe aur concise rehta hai.


---

## 📌 Q028: Visual Sliced Total vs Grand Total (`ALL` vs `ALLSELECTED`)

### 💡 Production DAX:
```dax
% of Selected Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE(
        [Total Net Sales],
        ALLSELECTED()
    ),
    0
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Jab user dashboard par slicer lagata hai, toh visual ke andar percentages ka sum 100% kyu nahi banta agar hum `ALL()` use karein? Aur ise 100% sum karne ke liye kya use karte hain?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, `ALL()` bahar ke slicers ko bhi uda deta hai jisse denominator pure dataset ka grand total ban jata hai. Visual ke andar 100% total lane ke liye hum `ALLSELECTED()` use karte hain, jo visual ke internal row grouping ko hatata hai lekin bahar ke slicers aur page filters ko preserve rakhta hai."*

### 🚨 Common Interview Traps:
* **Trap 1: Syntax of `ALLSELECTED`:** `ALLEXCEPT` table aur column dono mangta hai (`ALLEXCEPT(Table, Col)`). Lekin `ALLSELECTED` me ya toh blank chhodte hain `ALLSELECTED()`, ya sirf column dete hain `ALLSELECTED(DimProduct[Category])`, ya sirf table dete hain `ALLSELECTED(DimProduct)`. Table aur column dono ek sath pass karna syntax error deta hai.
* **Trap 2: Visual Totals Always Sum to 100%:** Production reports me matrix ya chart ka share of total dikhane ke liye standard best practice `ALLSELECTED()` hi hoti hai.


---

## 📌 Q029: Top N Aggregation (`TOPN` with `CALCULATE`)

### 💡 Production DAX:
```dax
Top 1 Product Sales = 
CALCULATE(
    [Total Net Sales],
    TOPN(
        1,
        VALUES(DimProduct[ProductName]),
        [Total Net Sales],
        DESC
    )
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Executive card visual me Top 1 best-selling product ka revenue kaise nikaloge? Aur worst-selling product nikalna ho toh kya change hoga?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, `TOPN` ek table function hai jo top N rows return karta hai. Ise `CALCULATE` ke filter argument me pass karte hain. Best-seller (Top 1) ke liye order parameter me `DESC` lagta hai (Highest to Lowest), aur agar worst-seller (Bottom 1) nikalna ho toh order parameter ko `ASC` (Lowest to Highest) kar dete hain."*

### 🚨 Common Interview Traps:
* **Trap 1: `ASC` vs `DESC`:** `TOPN` me default ya highest value ke liye `DESC` lagta hai. `ASC` lagane se bottom performer return hoga.
* **Trap 2: Ties in `TOPN`:** Agar do products ki sales barabar (tie) ho jaye, toh `TOPN(1, ...)` dono products return kar deta hai (2 rows). Strict single item ke liye secondary tie-breaker column diya jata hai.


---

## 📌 Q030: Top N Portfolio Concentration % (Pillar 1 Grand Finale)

### 💡 Production DAX:
```dax
-- Measure 1: Top 3 Combined Sales
Top 3 Products Sales = 
CALCULATE(
    [Total Net Sales],
    TOPN(
        3,
        VALUES(DimProduct[ProductName]),
        [Total Net Sales],
        DESC
    )
)

-- Measure 2: Concentration %
Top 3 Products Sales % = 
DIVIDE(
    [Top 3 Products Sales],
    CALCULATE([Total Net Sales], ALL(DimProduct)),
    0
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Pareto 80/20 analysis ke liye Top 3 Best-Selling products ka combined revenue aur unka overall revenue percentage share kaise nikaloge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, pehle hum `CALCULATE` ke andar `TOPN(3, VALUES(DimProduct[ProductName]), [Total Net Sales], DESC)` laga kar Top 3 products ka sales amount nikalenge.*
  > *Fir doosre measure me Measure Branching use karke `DIVIDE([Top 3 Products Sales], CALCULATE([Total Net Sales], ALL(DimProduct)), 0)` se overall portfolio concentration % calculate karenge."*

### 🚨 Common Interview Traps:
* **Trap 1: Comma and Syntax:** `CALCULATE` me measure ke baad comma lagana zaroori hota hai: `CALCULATE([Measure], TOPN(...))`.
* **Trap 2: Ascending vs Descending:** Top performers ke liye hamesha `DESC` order aayega, `ASC` lagane se bottom 3 products nikal aayenge.
* **Trap 3: Denominator Filter Clearing:** Denominator me `ALL(DimProduct)` lagana padta hai taaki pura catalog ka 100% baseline mil sake.


---

## 📌 Q031: KPI Target Status Badge (`SWITCH(TRUE())`)

### 💡 Production DAX:
```dax
Margin Health Status = 
SWITCH(
    TRUE(),
    ISBLANK([Profit Margin %]), "⚪ No Data",
    [Profit Margin %] >= 0.20, "🟢 High Margin",
    [Profit Margin %] >= 0.10, "🟡 Moderate Margin",
    "🔴 Low Margin"
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Executive dashboard me raw profit % ke sath-sath colored visual status badges (🟢 / 🟡 / 🔴) kaise display karoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, multiple condition badges ke liye hum `SWITCH(TRUE(), ...)` ladder pattern use karte hain.*
  > *Sabse pehle `ISBLANK()` check lagate hain taaki blank ko 0 na maan liya jaye. Fir highest threshold (`>= 0.20`) se lowest threshold evaluate karte hain."*

### 🚨 Common Interview Traps:
* **Trap 1: 20 vs 0.20 (Percentage Trap):** Power BI me 20% visually dikhta hai lekin DAX engine me wo `0.20` hota hai. `>= 20` likhne se wo 2000% check karega aur condition fail ho jayegi.
* **Trap 2: `ISBLANK` Function Call:** DAX me `ISBLANK(Measure)` function hota hai, SQL ya English jaisa `Measure ISBLANK` likhna syntax error deta hai.


---

## 📌 Q032: Dynamic Conditional Formatting via Hex Codes

### 💡 Production DAX:
```dax
Margin Color Code = 
SWITCH(
    TRUE(),
    ISBLANK([Profit Margin %]), "#BDC3C7", -- Neutral Gray
    [Profit Margin %] >= 0.20, "#2ECC71", -- Green
    [Profit Margin %] >= 0.10, "#F1C40F", -- Amber Yellow
    "#E74C3C"                             -- Red (Handles < 10% and Negative Losses)
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Visuals me background ya font color ko dynamically control karne ke liye DAX measure kaise likhoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, hum DAX me Hex Color Code string (`#RRGGBB`) return karne wala measure banate hain aur Power BI visual me Conditional Formatting me jaakar 'Format by Field Value' me is measure ko select kar lete hain.*
  > *Red color ko last me default fallback rakhte hain taaki low margin ke sath-sath negative losses bhi red me highlight ho sakein."*

### 🚨 Common Interview Traps:
* **Trap: Negative Margins (Losses):** Agar last me `[Profit Margin %] >= 0, "#E74C3C"` likha, toh jo product loss me hoga (-5% margin), wo blank ho jayega aur color nahi aayega. Isliye last argument ko hamesha direct fallback `"#E74C3C"` chhodte hain.


---

## 📌 Q033: Dynamic Arrow Indicators & Variance KPI (`FORMAT` + Unicode)

### 💡 Production DAX:
```dax
Variance Trend KPI = 
VAR VarianceVal = [Sales Variance %]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(VarianceVal), "-",
    VarianceVal > 0.02,  "▲ +" & FORMAT(VarianceVal, "0.0%"),
    VarianceVal < -0.02, "▼ "  & FORMAT(VarianceVal, "0.0%"),
    "► "  & FORMAT(VarianceVal, "0.0%")
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Card visual me sales growth ke sath directional arrows (▲ / ▼ / ►) aur percentage text ek sath kaise display karoge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, hum `SWITCH(TRUE(), ...)` ke sath DAX ka `FORMAT([Sales Variance %], '0.0%')` function use karte hain aur Unicode arrows (`▲`, `▼`, `►`) ko `&` operator se concatenate kar dete hain.*
  > *Variable (`VAR`) me measure ko cache kar lete hain taaki baar-baar recalculate na ho."*

### 🚨 Common Interview Traps:
* **Trap: `FORMAT()` Returns Text:** `FORMAT()` lagane ke baad measure ka data type **Text** ban jata hai! Ise chart ke numerical Y-axis par plot nahi kiya ja sakta, ye sirf Card visual, Matrix text labels, ya Tooltips ke liye hota hai.


---

## 📌 Q034: Dynamic Auto-Scaling Currency (`K` / `M` Formatting)

### 💡 Production DAX:
```dax
Formatted Sales Scaled = 
VAR SalesVal = [Total Net Sales]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(SalesVal), "₹ 0",
    SalesVal >= 1000000, "₹ " & FORMAT(SalesVal / 1000000, "0.0") & "M",
    SalesVal >= 1000,    "₹ " & FORMAT(SalesVal / 1000, "0.0") & "K",
    "₹ " & FORMAT(SalesVal, "#,##0")
)
```

### 🏢 Business Scenario:
* **Interviewer:** *"Card visuals me sales amount automatically K (Thousands) ya M (Millions) me dynamically kaise scale hoti hai?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, hum `SWITCH(TRUE(), ...)` ke sath numbers ko 1,000,000 ya 1,000 se divide karke `FORMAT(..., '0.0') & 'M'` suffix jod dete hain.*
  > *Sabse important ye hai ki `VAR SalesVal = [Total Net Sales]` use karein taaki ek hi card visual me measure 6-7 baar evaluate hone ke bajaye sirf 1 baar evaluate ho aur query 7x fast ho jaye."*

### 🚨 Common Interview Traps:
* **Trap 1: Performance without `VAR`:** Agar `VAR` use nahi kiya, toh DAX har condition aur division me `[Total Net Sales]` measure ko bar-bar calculate karega jisse CPU query load bohot badh jayega.
* **Trap 2: Modern Dynamic Format Strings:** Agar interviewer puche: *"Kya hum measure ko text banaye bina bhi ye kar sakte hain?"* Toh jawab hai: *"Haan sir, Power BI ke native 'Dynamic Format String' feature se `SELECTEDMEASURE()` use karke ise numeric rakha ja sakta hai."*


---

## 📌 Q035: Multi-Criteria Customer Segmentation (`SWITCH(TRUE())` with `&&`)

### 💡 Production DAX:
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

### 🏢 Business Scenario:
* **Interviewer:** *"Customer ko Spend aur Order Frequency dono ke basis par segment karke tier badge (Platinum, Gold, One-Time) kaise doge?"*
* **Aapka 1–2 Yr Analyst Professional Jawab:**
  > *"Sir, hum multiple measures ko `VAR __SalesVal` aur `VAR __OrdersVal` me cache karte hain aur `SWITCH(TRUE(), ...)` ke andar compound Boolean operator `&&` (AND) se dono conditions check karte hain.*
  > *Sabse pehle `ISBLANK` check karte hain, fir highest tier se lowest tier tak order evaluate karte hain."*

### 🚨 Common Interview Traps:
* **Trap: Measure vs Calculated Column for Slicers:** Ye measure table visual aur cards me kaam karega. Lekin agar interviewer puche ki *"Kya ise Slicer me daal kar page filter kar sakte hain?"*, toh jawab hai: *"Nahi sir, Slicer ke liye ise `DimCustomer` me Calculated Column banana padega."*
