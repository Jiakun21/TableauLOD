# Daily Profit KPI

```SQL
With Daily_Profit_KPI as (
Select 
    OrderDate,
    Case When Sum(Profit) > 2000 Then "Highly Profitable"
         When Sum(Profit) < 0 Then "Unprofitable"
         Else "Profitable"
    End as Profit_KPI
From lod3
Group by OrderDate)

Select 
    Profit_KPI,
    Year(OrderDate) as OrderYear, 
    Month(OrderDate) as OrderMonth, 
    Count(Distinct OrderDate) as cnt
From Daily_Profit_KPI
Group by Year(OrderDate), Month(OrderDate), Profit_KPI
Order by Profit_KPI, Year(OrderDate), Month(OrderDate)
```
