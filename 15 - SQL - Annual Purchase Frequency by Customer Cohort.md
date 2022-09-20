# Annual Purchase Frequency by Customer Cohort

```SQL
-- 1. Customer Cohort
With Customer_Cohort as (
Select CustomerID, Year(Min(OrderDate)) as CohortYear
From lod15
Group by CustomerID),

-- 2. Annual Purchase Freq
Annual_Purchase_Freq as (
Select 
    CustomerID, Year(OrderDate) as OrderYear, 
    Count(Distinct OrderID) as Freq
From lod15
Group by CustomerID, Year(OrderDate)
),

-- 3. Num Customers per Freq per CohortYeaer per OrderYear
Stats_Details as (
Select 
    CohortYear, OrderYear, Freq,
    Count(Distinct a.CustomerID) as Cnt
From Annual_Purchase_Freq a Left Join Customer_Cohort  c
	Using(CustomerID)
Group by CohortYear, OrderYear, Freq)

-- Final Results
-- Window Func. : at least N times
-- n : Num of Customers per Cohort
Select s.*,
    Round(Sum(s.Cnt) Over(Partition by s.CohortYear, OrderYear
                          Order by Freq Desc)/n.Cnt * 100) as Pct
From Stats_Details s Left Join (
            Select CohortYear, Count(CustomerID) as Cnt
            From Customer_Cohort
            Group by CohortYear) n Using(CohortYear)
Order by s.CohortYear, OrderYear, Freq
```
