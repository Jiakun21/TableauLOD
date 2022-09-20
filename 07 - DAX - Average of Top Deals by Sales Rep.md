# Average of Top Deals by Sales Rep

```DAX
Avg_Max_Sales = 
        IF(HASONEFILTER('DAX'[Country]),
           CALCULATE(AVERAGEX(SUMMARIZE('DAX', 'DAX'[Country], 'DAX'[Sales Rep], "MaxSales", Max('DAX'[Sales])),[MaxSales]),All(),VALUES('DAX'[Country])),
           CALCULATE(AVERAGEX(SUMMARIZE('DAX','DAX'[Sales Rep],"MaxSales_",Max('DAX'[Sales])),[MaxSales_]),All()))

BarColor_ = IF([MaxSale_] > [Avg_Max_Sales], 1, -1)

MaxSale_ = IF(HASONEFILTER('DAX'[Sales Rep]),
              Max('DAX'[Sales]),
              AVERAGEX(SUMMARIZE('DAX','DAX'[Sales Rep],"MaxSales",MAX('DAX'[Sales])),[MaxSales]))

Title2 = IF(HASONEVALUE('Dax'[Country]),SELECTEDVALUE('DAX'[Country]),"All")
```

# Result
