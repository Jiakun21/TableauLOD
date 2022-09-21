# Proportional Brushing

```DAX
Percent = CALCULATE([TotalSales])/CALCULATE([TotalSales],ALLEXCEPT('DAX','DAX'[Sub-Category]))

SalesSubCat = CALCULATE([TotalSales],ALLEXCEPT('DAX','DAX'[Sub-Category]))

SelCountry_ = SELECTEDVALUE('DAX'[Country],"NONE")

TotalSales = SUM('DAX'[Sales])
```

# Result

![DAX14](https://user-images.githubusercontent.com/79496040/191626528-f0f692f9-6589-4139-a201-8b35eb8a7df6.gif)
