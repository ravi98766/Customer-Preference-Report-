**Customer Performance Dashboard – Key Insights (Germany)**

**Customer Demographics**

•	The dataset consists of 1,780 customers from Germany, with an average customer age of 46 years, indicating a mature customer base.

•	Gender distribution is fairly balanced:
```
o	Female customers contribute 52.95% of total revenue ($16.05M).

o	Male customers contribute 47.05% of total revenue ($14.26M).
```
**Household Composition**

•	75% of customers have children, contributing approximately $22.66M in revenue, making this the dominant and most valuable customer segment.

•	25% of customers do not have children, contributing around $7.66M in revenue.

•	Customers with children show higher overall revenue contribution, suggesting stronger purchasing power or higher basket sizes.

**Revenue by Age Bracket**

•	The 51–60 age group generates the highest revenue ($8.0M), outperforming the average revenue benchmark of $6.06M.

•	The 31–40 ($7.6M) and 41–50 ($6.8M) age groups also exceed the average, making mid-aged to senior customers the primary revenue drivers.

•	The 21–30 and Over 70 segments contribute significantly less revenue, indicating lower spending intensity.

**Customer Value Segmentation**

•	Loyal Customers are the strongest segment, generating $26M, highlighting the importance of retention strategies.

•	VIP Customers contribute $3M, representing high-value but limited volume.

•	Periodic Customers contribute only $1M, suggesting an opportunity for targeted engagement and conversion into loyal segments.

**Top Customers**

•	A small group of top-ranked customers (e.g., Ricky Vazquez, Franklin Xu, Ricky Navas, Isabel Barnes) contributes disproportionately high revenue.

•	This reflects a Pareto-like distribution, where a few high-value customers drive a large share of sales.

**Business Implications**

•	Revenue growth strategies should focus on:
```
o	Customers aged 31–60

o	Households with children

o	Loyal customer retention and upselling
```
•	There is clear potential to increase revenue by:
```
o	Converting Periodic Customers into Loyal Customers

o	Expanding engagement with younger (21–30) and senior (70+) segments through tailored offerings.
```
<img width="893" height="507" alt="My Dashboard" src="https://github.com/user-attachments/assets/45303173-e937-47c8-9882-78dccf4ab4fe" />

_______________________________________________________________________________________________________________________________________________________________________________________



**DAX AND MEASURES**

```
#Customers = DISTINCTCOUNT(FactTable[CustomerKey])
```
```
% CustomerwithoutChildren = DIVIDE([Customer without Children Revenue],[Total Revenue])
```
```
% CWC = DIVIDE([Customer with Children(CWC) Revenue],[Total Revenue])
```
```
Average Customer age = average(DimCustomer[Customer Age])
```
```
Average Revenue(by Age Group) = 
AVERAGEX(
    ALL(
        DimCustomer[Age Category]
        ),
        [Total Revenue]
)
```
```
Avg Caption = 
VAR _AvgRevenueAgeGroup = 
Averagex(
    SUMMARIZE(DimCustomer,DimCustomer[Age Category]), 
    [Average Revenue(by Age Group)] 
)
VAR _RevenuefilteredTable= 
sumx(
    FILTER(
        SUMMARIZE(DimCustomer,DimCustomer[Age Category]), 
        [Total Revenue]>_AvgRevenueAgeGroup),
    [Total Revenue]
)
VAR _TopAgeGroup=
TOPN(
    1,
    DISTINCT(DimCustomer[Age Category]),
    [Total Revenue],desc, 
    DimCustomer[Age Category],asc)

VAR _RevnueTopAgegroup= 
CALCULATE([Total Revenue],_TopAgeGroup)

VAR _Result= DIVIDE(_RevenuefilteredTable,[Total Revenue])

return 
FORMAT(_Result,"00.0%") & " of Revenue is attributed to the yellow bars, Primarily led by the" &  _TopAgeGroup & " Age-Group Surpassing Average Revenue Line."
```
```
Caption Customers with children = 
VAR _NoofCountries= DISTINCTCOUNT(DimGeography[Country])
VAR _TopCountry= TOPN(1,DISTINCT(DimGeography[Country]),[Customer without Children Revenue],DESC,DimGeography[Country],asc)
VAR _TotalCustomersTopCountry= CALCULATE(DISTINCTCOUNT(FactTable[CustomerKey]),_TopCountry)
VAR _AllCustomerwithoutChildren=CALCULATE([Customer without Children Revenue],_TopCountry,ALL(DimGeography[Country]))
VAR _TotalRevenueTopCountry= CALCULATE([Customer without Children Revenue],_TopCountry)
VAR _pctCustomerwithoutchildren=DIVIDE(_TotalRevenueTopCountry, CALCULATE([Customer without Children Revenue],ALL(DimGeography[Country])))
VAR _MaleGenderwithoutchildren= CALCULATE([Customer without Children Revenue],_TopCountry, DimCustomer[Gender]="M")
VAR _pctMaleGenderwithoutchilden=DIVIDE(_MaleGenderwithoutchildren,_AllCustomerwithoutChildren)
VAR _FemaleGenderwithoutchildren= CALCULATE([Customer without Children Revenue],_TopCountry, DimCustomer[Gender]="F")
VAR _pctFemaleGenderwithoutchilden=DIVIDE(_FemaleGenderwithoutchildren,_AllCustomerwithoutChildren)

RETURN
upper(_TopCountry) & " leads with " & 
FORMAT(_pctCustomerwithoutchildren,"0.00%") & " of customer without children among " & 
FORMAT(_NoofCountries,"#,##" & " Countries, generating " &
FORMAT(_TotalRevenueTopCountry,"$#,##") & " in Revenue from " & 
FORMAT(_TotalCustomersTopCountry,"#,##") & " customer comprising " & 
FORMAT(_pctMaleGenderwithoutchilden,"0.00%") & " male and " &
FORMAT(_pctFemaleGenderwithoutchilden,"0.00%") & " female ")
```
```
Caption Customers without children = 
VAR _NoofCountries= DISTINCTCOUNT(DimGeography[Country])
VAR _TopCountry= TOPN(1,DISTINCT(DimGeography[Country]),[Customer with Children(CWC) Revenue],DESC,DimGeography[Country],asc)
VAR _TotalCustomersTopCountry= CALCULATE(DISTINCTCOUNT(FactTable[CustomerKey]),_TopCountry)
VAR _AllCustomerwithoutChildren=CALCULATE([Customer with Children(CWC) Revenue],_TopCountry,ALL(DimGeography[Country]))
VAR _TotalRevenueTopCountry= CALCULATE([Customer with Children(CWC) Revenue],_TopCountry)
VAR _pctCustomerwithoutchildren=DIVIDE(_TotalRevenueTopCountry, CALCULATE([Customer with Children(CWC) Revenue],ALL(DimGeography[Country])))
VAR _MaleGenderwithoutchildren= CALCULATE([Customer with Children(CWC) Revenue],_TopCountry, DimCustomer[Gender]="M")
VAR _pctMaleGenderwithoutchilden=DIVIDE(_MaleGenderwithoutchildren,_AllCustomerwithoutChildren)
VAR _FemaleGenderwithoutchildren= CALCULATE([Customer with Children(CWC) Revenue],_TopCountry, DimCustomer[Gender]="F")
VAR _pctFemaleGenderwithoutchilden=DIVIDE(_FemaleGenderwithoutchildren,_AllCustomerwithoutChildren)

RETURN
upper(_TopCountry) & " leads with " & 
FORMAT(_pctCustomerwithoutchildren,"0.00%") & " of customer with children among " & 
FORMAT(_NoofCountries,"#,##" & " Countries, generating " &
FORMAT(_TotalRevenueTopCountry,"$#,##") & " in Revenue from " & 
FORMAT(_TotalCustomersTopCountry,"#,##") & " customer comprising " & 
FORMAT(_pctMaleGenderwithoutchilden,"0.00%") & " male and " &
FORMAT(_pctFemaleGenderwithoutchilden,"0.00%") & " female ")
```


```
CF Age Group = if([Total Revenue]>[Average Revenue(by Age Group)], 1,0)
```
```

CF Female = if([Pct Male Rev]<[Pct Female Rev], "Green", "Red")
```
```
CF male = if([Pct Male Rev]>[Pct Female Rev], "Green", "Red")
```
```
Customer with Children(CWC) Revenue = 
VAR _Customerwithchildren= 
FILTER(
    DimCustomer, 
    DimCustomer[TotalChildren]>0
    )
RETURN
CALCULATE([Total Revenue],_Customerwithchildren)
```
```
Customer without Children Revenue = 
VAR _Customerwithoutchildren= 
FILTER(
    DimCustomer, 
    DimCustomer[TotalChildren]=0
    )
RETURN
CALCULATE([Total Revenue],_Customerwithoutchildren)
```
```
Dynamic Customers = 
VAR _TopCustomers =
    RANKX(
        ALL(DimCustomer[FullName]),
        [Total Revenue],
        ,
        DESC
    )
RETURN
IF(_TopCustomers <= 'Dynamic Customers'[Dynamic Customers Value], [Total Revenue])
```
```
Female Revenue = CALCULATE([Total Revenue], DimCustomer[Gender]="F")
```
```
Male Revenue = CALCULATE([Total Revenue], DimCustomer[Gender]="M")
```
```
Pct Female Rev = DIVIDE([Female Revenue],[Total Revenue])
```
```  
Pct Male Rev = DIVIDE([Male Revenue],[Total Revenue])  
```
```
subTitle for Top Customer = 
VAR _selectedCustomer=selectedvalue('Dynamic Customers'[Dynamic Customers])
Return
" The "  &  _selectedCustomer & " Highest Ranking Customer"
```
```
Total customers = [#Customers]
```
```
Total Revenue = 
sumx(
    FactTable,
    FactTable[OrderQuantity]*RELATED(DimProduct[ListPrice])
) 
```





