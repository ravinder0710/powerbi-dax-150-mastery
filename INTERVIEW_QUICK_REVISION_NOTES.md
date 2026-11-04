# 🎯 Power BI DAX Interview Master Guide (Human Language & Real Scenarios)
> **Target Role:** Data Analyst (0–2 Years Experience)  
> **Repository:** `powerbi-dax-150-mastery`  
> **Rule:** Koi kitabi jargon nahi. Sirf aam bol-chaal (Human Hinglish) me seekhna hai ki interviewer kya puchega aur hume room me baith kar kya jawab dena hai.

---

# 🟢 PILLAR 1: CORE DAX AGGREGATIONS & MATH

---

## 📌 Q001: Row-by-Row Calculation (`SUMX` vs `SUM`)

### 💡 Production Formula:
```dax
Total Net Sales = 
SUMX(
    FactSales, 
    (FactSales[Quantity] * FactSales[UnitPrice]) - FactSales[DiscountAmount]
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Maan lo hamari company me Sales ka column pehle se bana hua nahi hai. Sirf Quantity, Unit Price aur Discount diya hai. Hume har order ka net paisa nikalna hai. Kaise nikaaloge?"*
* **Aapka Jawab (Human Language me):**
  > *"Sir, company warehouse me faltu column store nahi karti taaki database ka kharcha bache. Hum Power BI me ek measure banayenge jo har row par ja kar `Quantity * Price` karega aur usme se discount minus karke total net sales calculate kar dega."*

### 🧠 Aasan Concept (Simple Dimaag me baithane ke liye):
* **`SUM`:** Ye ek seedha saadha function hai. Ye sirf ek single column ko upar se neeche tak jod sakta hai. Ye beech me guna ya minus nahi kar sakta.
* **`SUMX`:** Ye ek samajhdar function hai. Ye table ki pehli row par jata hai, multiply aur minus karta hai, fir doosri row par jata hai... aur aakhri me sabka total jod deta hai.

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal 1:** *"Aapne Fact table me Calculated Column kyu nahi bana liya?"*
  - **Aapka Jawab:** *"Sir, agar hum calculated column banate to wo hamari RAM aur hard drive me space gher leta. Agar table me 10 lakh rows hain, to file ka size bada ho jata. Measure use karne se koi space nahi lagti, wo screen par click karte hi turant calculate ho jata hai."*
* **Trap Sawal 2:** *"Columns ke aage Table ka naam kyu likha hai?"*
  - **Aapka Jawab:** *"Taaki kal ko koi doosra banda ya main khud 6 mahine baad code padhu, to turant samajh aa jaye ki ye data table ka physical column hai aur koi measure nahi hai."*

---

## 📌 Q002: Measure Branching (`Total Cost` & `Total Profit`)

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

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Management ko ye janna hai ki humne discount dene ke baad actual me kitna munafa (Profit) kamaya. Aur Profit nikalte waqt poora lamba formula dubara mat likhna. Kaise karoge?"*
* **Aapka Jawab:**
  > *"Sir, hum pehle 'Total Cost' ka chhota measure banayenge. Fir hum 'Measure Branching' use karenge, yaani pehle se bane hue Net Sales me se Cost ko seedha minus kar denge: `[Total Net Sales] - [Total Cost]`."*

### 🧠 Aasan Concept:
* Jaise hum bachpan me maths me karte the: Pehle step 1 solve kiya, fir step 2, fir dono ko minus kar diya. Har baar shuru se lamba formula likhne ki zaroorat nahi hoti.

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"Ek hi lambe formula me sab kuch kyu nahi likh diya?"*
  - **Aapka Jawab:** *"Sir, agar kal ko company ne discount ka koi naya rule bana diya, to mujhe sirf Net Sales ka formula theek karna padega. Mera Profit apne aap theek ho jayega! Agar lamba formula likhta, to mujhe 10 jagah ja kar code dhoondh ke theek karna padta."*

---

## 📌 Q003: Safe Division (`DIVIDE` vs `/`)

### 💡 Production Formula:
```dax
Profit Margin % = 
DIVIDE(
    [Total Profit], 
    [Total Net Sales], 
    0
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Hume Profit Margin % nikalna hai. Lekin kuch orders cancel ho gaye the jinki Sales 0 hai. Agar hum divide karenge to report crash ho sakti hai. Isko kaise handle karoge?"*
* **Aapka Jawab:**
  > *"Sir, maths ka rule hai ki kisi bhi number ko 0 se divide karo to error aata hai. Isliye hum simple slash `/` nahi lagayenge, hum Power BI ka `DIVIDE()` function use karenge jo 0 aane par error nahi dega, balki safely 0 ya Blank dikha dega."*

### 🧠 Aasan Concept:
* `/` (Slash) ek laparwah driver hai, gaddha aate hi gaadi thonk dega.
* `DIVIDE()` ek safe driver hai, gaddha dekhte hi gaadi rok lega aur crash nahi hone dega.

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal 1:** *"Agar 3rd parameter (0) na likhein to kya hoga?"*
  - **Aapka Jawab:** *"Sir, default me `BLANK()` aayega."*
* **Trap Sawal 2:** *"0 likhna achha hai ya Blank chhodna?"*
  - **Aapka Jawab:** *"Sir, Blank chhodna zyada achha hai! Kyunki Power BI report me jahan Blank hota hai, wo un khali rows ko screen se apne aap chupa deta hai, jisse report bohot fast chalti hai. Agar 0 likhenge to Power BI ko bekar ki hazaron rows screen par draw karni padengi."*

---

## 📌 Q004: Distinct Counting (`DISTINCTCOUNT` vs `COUNTROWS`)

### 💡 Production Formula:
```dax
Total Orders Placed = 
DISTINCTCOUNT(FactSales[OrderID])
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Ek customer ne Amazon se ek sath 3 cheezein mangwai—ek t-shirt, ek joota, ek ghadi. Database me 3 rows ban gayi same Order ID ke sath. Ab mujhe ye batao ki customer ne kitne order diye?"*
* **Aapka Jawab:**
  > *"Sir, customer ne to 1 hi order diya tha, bhale hi usme 3 items the. Agar hum rows gin lenge to 3 order dikhenge jo galat hai. Isliye hum `DISTINCTCOUNT(OrderID)` lagayenge taaki unique orders count hon."*

### 🧠 Aasan Concept:
* `COUNTROWS`: Kitne dabbe bache (Total rows).
* `DISTINCTCOUNT`: Kitne alag-alag grahak the (Unique values).

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal 1:** *"Agar kisi row me Order ID khali (Blank) ho, to kya DISTINCTCOUNT use count karega?"*
  - **Aapka Jawab:** *"Haan sir, DISTINCTCOUNT blank ko bhi ek category maankar +1 jod leta hai. Agar hume khali order nahi ginne, to hum `DISTINCTCOUNTNOBLANK()` use karenge."*
* **Trap Sawal 2:** *"Puri table ki rows ginni ho to `COUNT(Column)` lagayein ya `COUNTROWS(Table)`?"*
  - **Aapka Jawab:** *"Sir, `COUNTROWS(Table)` lagana chahiye kyunki wo table ke dhabbe scan nahi karta, wo seedha table ki diary (metadata) se number padh leta hai. Isliye wo superfast hota hai."*

---

## 📌 Q005: Average Order Value (AOV)

### 💡 Production Formula:
```dax
Average Order Value = 
DIVIDE(
    [Total Net Sales], 
    [Total Orders Placed], 
    0
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Store owner ko dekhna hai ki ek grahak average kitne rupaye ki khareedari karke jata hai. Direct `AVERAGE` function kyu nahi use kar sakte yahan?"*
* **Aapka Jawab:**
  > *"Sir, direct `AVERAGE` function ek item ka average nikalta hai, pooray order ka nahi. Maan lo customer ne ₹ 900 ka joota aur ₹ 50 ke moje liye, to AVERAGE function dono ko jod kar ₹ 475 average bata dega, jabki grahak ne ₹ 950 kharch kiye the! Isliye Total Sales ko Total Unique Orders se divide karna hi sahi business tareeqa hai."*

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"Kya hum DAX me `AVERAGE([Total Net Sales])` likh sakte hain?"*
  - **Aapka Jawab:** *"Nahi sir! Power BI seedha error de dega. AVERAGE function ke andar sirf table ka column daal sakte hain, koi measure nahi daal sakte."*

---

## 📌 Q006: Filter Lagana (`CALCULATE`)

### 💡 Production Formula:
```dax
Electronics Net Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics"
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Mujhe dashboard par ek card visual chahiye jisme chahe user kuch bhi dabaye, mujhe sirf aur sirf 'Electronics' ki hi sales dikhni chahiye. Ye kaise karoge?"*
* **Aapka Jawab:**
  > *"Sir, iske liye hum `CALCULATE` function use karenge aur uske andar filter daal denge `Category = 'Electronics'`. Ye purane saare filters ko daba kar sirf electronics ka dhandha calculate karega."*

### 🧠 Aasan Concept:
* `CALCULATE` DAX ka **Bahubali** hai. Baaki functions sirf wahi calculate karte hain jo visual bolta hai. Lekin `CALCULATE` ke paas taqat hai ki wo visual ke filter ko badal de ya naya filter laga de.

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal 1:** *"Aapne `CALCULATE(SUM([Total Net Sales]), ...)` kyu nahi likha?"*
  - **Aapka Jawab:** *"Sir, `[Total Net Sales]` to pehle se measure hai. Measure ke upar dobara SUM lagane se syntax error aa jata hai."*
* **Trap Sawal 2:** *"CALCULATE ke andar pehle calculation hoti hai ya pehle filter lagta hai?"*
  - **Aapka Jawab:** *"Sir, pehle filter lagta hai! Pehle data chhan kar chhota hota hai, fir aakhri me uske upar sales calculate hoti hai."*

---

## 📌 Q007: Multiple Filters (Comma `,` vs `&&`)

### 💡 Production Formula:
```dax
North Electronics Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] = "Electronics", 
    DimStore[Region] = "North"
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Mujhe do conditions ek sath lagani hain: Product sirf 'Electronics' ho aur Store sirf 'North' region ka ho. Comma lagana achha hai ya `&&` lagana?"*
* **Aapka Jawab:**
  > *"Sir, hamesha Comma `,` lagana chahiye. Comma lagane se Power BI dono tables ko alag-alag parallel scan karta hai aur jaldi answer deta hai. Agar hum `&&` lagayenge to Power BI thoda slow ho sakta hai."*

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"Agar dono ka output same aata hai to interviewer Comma kyu expect karta hai?"*
  - **Aapka Jawab:** *"Kyunki alag-alag tables ke columns par Comma lagana industry standard best practice hai taaki DAX engine par faltu load na pade."*

---

## 📌 Q008: Ek se zyada cheezein chunna (`IN` Operator)

### 💡 Production Formula:
```dax
Electronics & Home Decor Sales = 
CALCULATE(
    [Total Net Sales], 
    DimProduct[Category] IN { "Electronics", "Home Decor" }
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Mujhe do categories 'Electronics' aur 'Home Decor' ka combined revenue ek sath nikalna hai. Isko clean tareeqe se kaise likhoge?"*
* **Aapka Jawab:**
  > *"Sir, hum `IN` operator use karenge aur curly brackets ke andar dono categories daal denge: `Category IN { 'Electronics', 'Home Decor' }`."*

### 🧠 Aasan Concept:
* `||` (OR) likhne me bar-bar `Category = ... || Category = ...` likhna padta hai jo dekhne me bohot lamba lagta hai. `IN` operator ek jhatke me saari list check kar leta hai.

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"SQL me to hum gol bracket `('A', 'B')` lagate hain, yahan curly bracket `{}` kyu lagaya?"*
  - **Aapka Jawab:** *"Sir, DAX me curly bracket `{}` ko Table Constructor bolte hain. DAX is list ko background me ek choti temporary table maanta hai, isliye DAX me curly bracket lagana compulsory hota hai."*

---

## 📌 Q009: Filters Hatana (`ALL`)

### 💡 Production Formula:
```dax
All Products Sales = 
CALCULATE(
    [Total Net Sales], 
    ALL(DimProduct)
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Ek table me har category ke samne mujhe Company ka poora Grand Total dikhana hai benchmark ke liye. Lekin table to har row ko filter kar deti hai. Filter kaise hataoge?"*
* **Aapka Jawab:**
  > *"Sir, visual ke filter ko hatane ke liye hum `ALL()` function use karenge. `ALL(DimProduct)` saare product filters ko mita deta hai aur poori company ka Grand Total return karta hai."*

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"Agar report par `Color = 'Red'` ka slicer laga ho, to `ALL(Category)` aur `ALL(DimProduct)` me kya farak padega?"*
  - **Aapka Jawab:** *"Sir, agar `ALL(Category)` likhenge to Color ka slicer total ko filter kar dega. Lekin agar `ALL(DimProduct)` likhenge, to poori table saaf ho jayegi aur chahe koi bhi slicer lagao, Grand Total kabhi nahi badlega."*

---

## 📌 Q010: Percentage of Total (% Share)

### 💡 Production Formula:
```dax
% of Total Sales = 
DIVIDE(
    [Total Net Sales],
    CALCULATE([Total Net Sales], ALL(DimProduct)),
    0
)
```

### 🏢 Interviewer Business Problem kaise puchega:
* **Interviewer:** *"Mujhe har category ka Market Share % chahiye, yaani us category ne company ke total dhandhe me kitne percent yogdan diya. Kaise nikaaloge?"*
* **Aapka Jawab:**
  > *"Sir, hum Category ki Sales ko Company ke Grand Total se divide kar denge. Upar aayega normal `[Total Net Sales]` aur neeche aayega `CALCULATE([Total Net Sales], ALL(DimProduct))` jisse hume har category ka % share mil jayega."*

### 🧠 Aasan Concept:
* Upar: Mera hissa (Current category sales).
* Neeche: Poori kheer (All products grand total).
* Dono ko divide kiya to nikal gaya mera % hissa!

### 🚨 Common Interview Traps & Human Answers:
* **Trap Sawal:** *"Matrix table ke subtotal par ye 100% dega ya Grand Total par?"*
  - **Aapka Jawab:** *"Sir, `ALL()` poore Grand Total par 100% dega. Agar user chahta hai ki Matrix ke subtotal par 100% bane, to hume `ALLSELECTED()` use karna padega."*
