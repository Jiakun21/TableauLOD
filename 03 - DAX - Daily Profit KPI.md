# Daily Profit KPI

```DAX
Dates = CALENDAR(MIN('DAX'[OrderDate]),MAX('DAX'[OrderDate]))

DAX = 
Var Daily_Profit = SUMMARIZE('Raw','Raw'[OrderDate],"Daily_Profit",SUM('Raw'[Profit]))
Return ADDCOLUMNS(Daily_Profit,"Profit_KPI",SWITCH(TRUE,[Daily_Profit] > 2000,"Highly Profitable", [Daily_Profit] < 0, "Unprofitable", "Profitable"))
```

# Result

![DAX03](https://user-images.githubusercontent.com/79496040/191813846-adf5c6bd-d4a6-458e-9e85-0eaa941a1261.gif)
