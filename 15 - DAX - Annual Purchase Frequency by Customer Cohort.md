# Annual Purchase Frequency by Customer Cohort

```DAX
DAX-T = 
Var Customer_Cohort = 
    ADDCOLUMNS(GROUPBY('DAX','DAX'[CustomerID]),
               "CohortYear",[CohortYear])
Var Annual_Purchase_Freq = 
    ADDCOLUMNS(GROUPBY('DAX','DAX'[CustomerID],'DAX'[OrderYear]),
               "Freq", [Freq])
Var Stats = 
    GROUPBY(NATURALLEFTOUTERJOIN(Annual_Purchase_Freq,Customer_Cohort),
            [OrderYear],[CohortYear],[Freq],
            "NumCust",COUNTX(CURRENTGROUP(),1))
Return GROUPBY(NATURALLEFTOUTERJOIN(Stats,Customer_Cohort),
               [OrderYear],[CohortYear],[Freq],[NumCust],
               "CustCohort",COUNTX(CURRENTGROUP(),1))

CumCust = CALCULATE(SUM('DAX-T'[NumCust]),
                    FILTER(ALLEXCEPT('DAX-T','DAX-T'[CohortYear],'DAX-T'[OrderYear]),
                           'DAX-T'[Freq]>=MAX('DAX-T'[Freq])))

Pct = [CumCust]/SUM('DAX-T'[CustCohort])

CohortYear = YEAR(CALCULATE(MIN('DAX'[OrderDate]),ALLEXCEPT('DAX','DAX'[CustomerID])))

DistCnt = DISTINCTCOUNT('DAX'[CustomerID])

Freq = DISTINCTCOUNT('DAX'[OrderID])

OrderYear = YEAR('DAX'[OrderDate])
```

# Result

![DAX15](https://user-images.githubusercontent.com/79496040/191603726-8fb2c907-d85e-465e-b123-78a727919eba.gif)

### Notes

Power BI might have some issues customizing tooltips in Line chart with Legend. This issue does not happend in Line and Clustered Column chart with Legend.
