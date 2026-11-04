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
