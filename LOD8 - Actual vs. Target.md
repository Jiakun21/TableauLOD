# Actual vs. Target

Link: https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD8-Actualvs_Target/Dashboard

```SQL

-- Group by at different levels + Join

With Stats as (
Select 
    State, 
    Sum(Profit - TargetProfit) as Diff,  
    Count(Distinct Product) as Total_Products
From lod8
Group by State),
Num_Profit_Above_Target as (
Select 
    State, Product,
    Case When Sum(Profit - TargetProfit) > 0 Then 1 Else 0 End as Profit_Above_Target
From lod8
Group by State, Product)

Select 
    s.State, Diff, 
    Round(Sum(n.Profit_Above_Target)/s.Total_Products * 100, 2) as Pct_Profit_Above_Target
From Stats s Join Num_Profit_Above_Target n Using(State)
Group by State
Order by Diff Desc;

-- 2nd Chart SQL Query:

Select 
    Product, 
    Sum(Profit) as Profit, 
    Sum(TargetProfit) as TargetProfit
From lod8
Where State = 'Iowa'
Group by Product
Order by Sum(Profit) Desc
```

## Result

<div class='tableauPlaceholder' id='viz1660588491466' style='position: relative'><noscript><a href='#'><img alt=' '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD8-Actualvs_Target&#47;Dashboard&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
