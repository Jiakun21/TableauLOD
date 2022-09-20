# Daily Profit KPI

```DAX
Dates = CALENDAR(MIN('DAX'[OrderDate]),MAX('DAX'[OrderDate]))

DAX = 
Var Daily_Profit = SUMMARIZE('Raw','Raw'[OrderDate],"Daily_Profit",SUM('Raw'[Profit]))
Return ADDCOLUMNS(Daily_Profit,"Profit_KPI",SWITCH(TRUE,[Daily_Profit] > 2000,"Highly Profitable", [Daily_Profit] < 0, "Unprofitable", "Profitable"))
```

# Result
