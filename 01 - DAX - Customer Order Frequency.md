# Customer Order Frequency

```DAX
Freq = 
Var Orders_per_Customer = SUMMARIZE('DAX','DAX'[CustomerID],"Total_Orders",DISTINCTCOUNT('DAX'[OrderID]))
Return SUMMARIZE(Orders_per_Customer, [Total_Orders], "Frequency", DISTINCTCOUNT('DAX'[CustomerID]))
```

# Result

![DAX01](https://user-images.githubusercontent.com/79496040/191597514-1e356c04-0f2e-4326-914c-8b923c257069.gif)
