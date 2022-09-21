# Proportional Brushing

```DAX
Percent = CALCULATE([TotalSales])/CALCULATE([TotalSales],ALLEXCEPT('DAX','DAX'[Sub-Category]))

SalesSubCat = CALCULATE([TotalSales],ALLEXCEPT('DAX','DAX'[Sub-Category]))

SelCountry_ = SELECTEDVALUE('DAX'[Country],"NONE")

TotalSales = SUM('DAX'[Sales])
```

# Result
