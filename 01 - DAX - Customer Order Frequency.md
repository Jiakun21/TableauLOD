# Customer Order Frequency

```DAX
Freq = 
Var Orders_per_Customer = SUMMARIZE('DAX','DAX'[CustomerID],"Total_Orders",DISTINCTCOUNT('DAX'[OrderID]))
Return SUMMARIZE(Orders_per_Customer, [Total_Orders], "Frequency", DISTINCTCOUNT('DAX'[CustomerID]))
```

# Result
