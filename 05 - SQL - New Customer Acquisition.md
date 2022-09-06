# New Customer Acquisition

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
