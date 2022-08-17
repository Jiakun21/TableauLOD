# [Proportional Brushing](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD14-ProportionalBrushing/Dashboard)

```SQL
-- Sol 1:

Select 
    SubCategory,
    Sum(Sum(If(Country = 'Australia', Sales, 0))) Over() as TotalSales,
    Sum(If(Country = 'Australia', Sales, 0)) as Sales_per_Country,
    Sum(Sales) as Sales,
    Round(Sum(If(Country = 'Australia', Sales, 0))/Sum(Sales) * 100, 2) as Pct
From lod14
Group by Market, SubCategory
Order by Sales_per_Country Desc;

-- Sol 2:

Select 
    SubCategory, Country,
    Sum(Sum(Sales)) Over(Partition by Country) as Sales_per_Country,
    Sum(Sales) as Sales_per_Country_per_SubCategory,
    Sum(Sum(Sales)) Over(Partition by SubCategory) as Sales_per_SubCategory,
    Round(Sum(Sales)/Sum(Sum(Sales)) Over(Partition by SubCategory) * 100, 2) as Pct
From lod14
Group by Market, Country, SubCategory
Order by Country, Sales_per_Country_per_SubCategory Desc
```

## Result

<div class='tableauPlaceholder' id='viz1660759817212' style='position: relative'><noscript><a href='#'><img alt=' '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD14-ProportionalBrushing&#47;Dashboard&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
