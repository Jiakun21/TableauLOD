# Cohort Analysis

```DAX
CohortYear = YEAR(CALCULATE(MIN('DAX'[OrderDate]), ALLEXCEPT('DAX', 'DAX'[CustomerID])))

OrderYear = YEAR('DAX'[OrderDate])

Pct_ = 
Var SalesTotal = SUM('DAX'[Sales])
Var CohortTotal = CALCULATE(SUM('DAX'[Sales]), ALL('DAX'[CohortYear]))
Return DIVIDE(SalesTotal,CohortTotal)
```

# Result

![DAX02](https://user-images.githubusercontent.com/79496040/191820713-186895f8-8ccc-44b6-9821-34d2e7da4922.gif)
