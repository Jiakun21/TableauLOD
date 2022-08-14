# New Customer Acquisition

Link: https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD5-NewCustomerAcquisition_16589761093450/NewCustomerAcquisition

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

## Result

<div class='tableauPlaceholder' id='viz1660501566869' style='position: relative'><noscript><a href='#'><img
                alt='New Customer Acquisition-- What is the total number of customers we’ve acquired per market by day? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD5-NewCustomerAcquisition_16589761093450&#47;NewCustomerAcquisition&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
