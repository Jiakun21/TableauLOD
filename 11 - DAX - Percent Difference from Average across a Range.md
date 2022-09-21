# Percent Difference from Average across a Range

```DAX
Avg_Price = CALCULATE(AVERAGE('DAX'[Close]),Filter(ALL('DAX'[Date]),'DAX'[Date]>=MIN('Date'[Date]) && 'DAX'[Date]<=MAX('Date'[Date])))

Pct_Diff = (SUM('DAX'[Close])-[Avg_Price])/[Avg_Price]

Date = CALENDAR(MIN('DAX'[Date]),MAX('DAX'[Date]))
```

# Result

![DAX11](https://user-images.githubusercontent.com/79496040/191629230-b3567414-6554-41c9-8df3-562f9459db15.gif)
