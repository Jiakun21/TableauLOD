# Customer Order Frequency

```sql
With Orders_per_Customer as (
Select CustomerID, Count(Distinct OrderID) as Total_Orders
From lod1
Group by CustomerID)

Select Total_Orders, Count(Distinct CustomerID) as Frequency
From Orders_per_Customer
Group by Total_Orders
```
