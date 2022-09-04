# [Daily Profit KPI](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD3-DailyProfitKPI_16588909744770/DailyProfitKPI)

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

## Result

<div class='tableauPlaceholder' id='viz1660495707622' style='position: relative'><noscript><a href='#'><img
                alt='Daily Profit KPI-- How many days each month are highly profitable, profitable, or unprofitable? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD3-DailyProfitKPI_16588909744770&#47;DailyProfitKPI&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
