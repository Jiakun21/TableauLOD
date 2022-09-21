# Percent Difference from Average across a Range

```DAX
Avg_Price = CALCULATE(AVERAGE('DAX'[Close]),Filter(ALL('DAX'[Date]),'DAX'[Date]>=MIN('Date'[Date]) && 'DAX'[Date]<=MAX('Date'[Date])))

Pct_Diff = (SUM('DAX'[Close])-[Avg_Price])/[Avg_Price]

Date = CALENDAR(MIN('DAX'[Date]),MAX('DAX'[Date]))
```

# Result
