# Actual vs. Target

```SQL
Select 
    State, 
    Sum(Diff_by_Prod) as Diff,
    Round(Sum(Profit_Above_Target)/Count(Product) * 100, 2) as Pct_Profit_above_Target
From (
	Select 
		State, Product,
		Sum(Profit - TargetProfit) as Diff_by_Prod,
		Case When Sum(Profit - TargetProfit) > 0 Then 1 Else 0 End as Profit_above_Target
	From lod8
	Group by State, Product) Num_Profit_above_Target
Group by State
Order by Diff Desc;

-- 2nd Chart SQL Query:

Select 
    Product, 
    Sum(Profit) as Profit, 
    Sum(TargetProfit) as TargetProfit
From lod8
-- Where State = 'Iowa'
Group by Product
Order by Sum(Profit) Desc
```
