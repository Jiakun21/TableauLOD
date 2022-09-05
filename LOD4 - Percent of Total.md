# [Percent of Total](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD4-PercentofTotal_16589750230440/PercentofTotal)

```sql
Select Country, Market,
    Round(Sum(Sales)/Sum(Sum(Sales)) Over() * 100) as Percent
From lod4
Group by Country, Market
```

## Result

<div class='tableauPlaceholder' id='viz1660499543183' style='position: relative'><noscript><a href='#'><img
                alt='Percent of Total-- For a given market what is each country’s percent of total global sales? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD4-PercentofTotal_16589750230440&#47;PercentofTotal&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
