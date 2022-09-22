# Value on the Last Day of a Period

```DAX
Avg_Price = AVERAGE('DAX'[Close])

BG_ = SWITCH(SELECTEDVALUE('DAX'[Ticker]),"FIRE","#ff7f0e","SYMC","#ffc156","#17becf")

LastDay = MAX('DAX'[Date])

LastDay_Price = CALCULATE([Avg_Price],'DAX'[Date] = MAX('DAX'[Date]))

Year_Mon = DATE(YEAR('DAX'[Date]),MONTH('DAX'[Date]),1)
```

# Result

![DAX09](https://user-images.githubusercontent.com/79496040/191636020-2c2d32f2-24bc-4f35-a07d-f890ed2f76e6.gif)
