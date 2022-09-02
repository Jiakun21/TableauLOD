# [Cohort Analysis](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD2-CohortAnalysis_16588895834750/CohortAnalysis)

```sql
With Customer_Cohort as (
Select Year(OrderDate) as OrderYear, Sales, Market,
	Year(Min(OrderDate) Over(Partition by CustomerID)) as Cohort
From lod2)

Select OrderYear, Cohort, Market, Round(Sum(Sales)/1000) as Total
From Customer_Cohort
Where Market = 'EMEA'
Group by OrderYear, Cohort, Market
Order by OrderYear, Cohort



-- Add Percent

Select OrderYear, Cohort, Market, 
    Round(Sum(Sales)/1000) as Total,
    Round(Sum(Sales)/Sum(Sum(Sales)) Over(Partition by OrderYear)*100, 2) as Percent
From Customer_Cohort
Where Market = 'EMEA'
Group by OrderYear, Cohort, Market
Order by OrderYear, Cohort
```

## Result

<div class='tableauPlaceholder' id='viz1660440872436' style='position: relative'><noscript><a href='#'><img
                alt='Cohort Analysis-- How much revenue is contributed annually by each customer cohort? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD2-CohortAnalysis_16588895834750&#47;CohortAnalysis&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
