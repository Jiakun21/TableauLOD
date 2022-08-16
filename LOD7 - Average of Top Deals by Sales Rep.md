# [Average of Top Deals by Sales Rep](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD7-AverageofTopDealsbySalesRep/Dashboard)

```SQL
Select 
    Country, SalesRep,
    Round(Max(Sales)) as Top_Deal,
    Round(Avg(Max(Sales)) Over(Partition by Country)) as Avg_Top_Deal
From lod7
-- Where Country Like 'Mau%'
Group by Country, SalesRep
Order by Country, Top_Deal Desc

-- Group by + Window Func
```

## Result

<div class='tableauPlaceholder' id='viz1660587248349' style='position: relative'><noscript><a href='#'><img alt=' '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD7-AverageofTopDealsbySalesRep&#47;Dashboard&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
