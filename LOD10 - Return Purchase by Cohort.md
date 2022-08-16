# [Return Purchase by Cohort](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD10-ReturnPurchasebyCohort_16593716058910/ReturnPurchasebyCohort)

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

## Result

<div class='tableauPlaceholder' id='viz1660676980071' style='position: relative'><noscript><a href='#'><img alt=' '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD10-ReturnPurchasebyCohort_16593716058910&#47;ReturnPurchasebyCohort&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
