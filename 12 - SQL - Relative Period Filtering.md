# Relative Period Filtering

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
