# [Relative Period Filtering](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD12-RelativePeriodFiltering/RelativePeriodFiltering)

```SQL
-- Bottom Chart SQL Query:

With Rolling_Profit as (
Select 
    Year(OrderDate) as OrderYear, 
    Week(OrderDate) as OrderWeek,
    Round(Sum(Sum(Profit)) Over(Partition by Year(OrderDate) Order by OrderDate), 2) as Cumulative_Profit
From lod12
Where DayofYear(OrderDate) <= (Select DayofYear(Max(OrderDate)) From lod12)
Group by Year(OrderDate), Week(OrderDate)
)

-- Top Chart SQL Query:

Select r1.OrderYear, r1.OrderWeek, 
    Round(r1.Cumulative_Profit - r2.Cumulative_Profit, 2) as Diff
From Rolling_Profit r1 Join Rolling_Profit r2
	on r1.OrderYear = r2.OrderYear + 1 and r1.OrderWeek = r2.OrderWeek
```

## Result

<div class='tableauPlaceholder' id='viz1660755006486' style='position: relative'><noscript><a href='#'><img
                alt='Relative Period Filtering-- What is the year-to-date profit of this year versus last year, up to the maximum day in the data source, August 23, 2014, in Week  34? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD12-RelativePeriodFiltering&#47;RelativePeriodFiltering&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
