# New Customer Acquisition

-- Sol 1:

```SQL
Select Distinct Market, OrderDate,
    Count(CustomerID) Over(Partition by Market Order by OrderDate) as Cumulative_DistCnt
From (
	Select *,
		Min(OrderDate) Over(Partition by CustomerID) as first_purchase
	From lod5) Customer_Cohort
Where OrderDate = first_purchase
Group by Market, OrderDate, CustomerID

-- Group by + Window Func. to deal with Distinct Count of Customers per Market per Date
```


-- Sol 2:

```SQL
With Customer_Cohort as (
	Select Market, OrderDate, CustomerID,
	Min(OrderDate) Over(Partition by CustomerID) as First_Purchase
From lod5)

Select 
	Market,
	OrderDate,
	Sum(Count(Distinct CustomerID)) Over(Partition by Market Order by OrderDate) as Cumulative_DistCnt
From Customer_Cohort
Where OrderDate = First_Purchase
Group by Market, OrderDate
Order by Market, OrderDate
```

