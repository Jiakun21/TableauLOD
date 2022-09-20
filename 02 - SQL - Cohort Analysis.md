# Cohort Analysis

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
