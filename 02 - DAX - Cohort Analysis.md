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
