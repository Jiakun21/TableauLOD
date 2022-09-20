# Value on the Last Day of a Period

```DAX
Avg_Price = AVERAGE('DAX'[Close])

BG_ = SWITCH(SELECTEDVALUE('DAX'[Ticker]),"FIRE","#ff7f0e","SYMC","#ffc156","#17becf")

LastDay = MAX('DAX'[Date])

LastDay_Price = CALCULATE([Avg_Price],'DAX'[Date] = MAX('DAX'[Date]))

Year_Mon = DATE(YEAR('DAX'[Date]),MONTH('DAX'[Date]),1)
```

# Result
