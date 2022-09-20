# Return Purchase by Cohort

```SQL
With Temp as (
Select *,
    Dense_Rank() Over(Partition by CustomerID Order by OrderDate) as rnk
From lod10),
Return_Purchase as (
Select Distinct 
    f.CustomerID, First_Purchase, Second_Purchase,
    Concat(Year(First_Purchase), '-Q', Quarter(First_Purchase)) as Year_Quarter,
    (Year(Second_Purchase) - Year(First_Purchase)) * 4 + (Quarter(Second_Purchase) - Quarter(First_Purchase)) as Diff
From 
	(Select CustomerID, OrderDate as First_Purchase
	 From Temp
	 Where rnk = 1) f Left Join
	(Select CustomerID, OrderDate as Second_Purchase
	 From Temp
	 Where rnk = 2) s Using(CustomerID)
)

Select Year_Quarter, Diff, Count(Distinct CustomerID) as cnt
From Return_Purchase
Group by Year_Quarter, Diff


-- Sol 2: Performance is much better!!!

With First_Purchase as (
Select CustomerID, Min(OrderDate) as First_Purchase
From lod10
Group by CustomerID),
Return_Purchase as (
Select f.CustomerID, First_Purchase, Min(OrderDate) as Second_Purchase
From First_Purchase f Left Join lod10 l
	on f.CustomerID = l.CustomerID and First_Purchase < OrderDate
Group by f.CustomerID)

Select 
    Concat(Year(First_Purchase), '-Q', Quarter(First_Purchase)) as Year_Quarter,
    (Year(Second_Purchase) - Year(First_Purchase)) * 4 + (Quarter(Second_Purchase) - Quarter(First_Purchase)) as Diff,
    Count(Distinct CustomerID) as cnt
From Return_Purchase
Group by 
    Concat(Year(First_Purchase), '-Q', Quarter(First_Purchase)),
    (Year(Second_Purchase) - Year(First_Purchase)) * 4 + (Quarter(Second_Purchase) - Quarter(First_Purchase))
```
