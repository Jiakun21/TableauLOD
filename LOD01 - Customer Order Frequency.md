# [Customer Order Frequency](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD1-CustomerOrderFrequency_16588880333730/CustomerOrderFrequency)

```sql
With Orders_per_Customer as (
Select CustomerID, Count(Distinct OrderID) as Total_Orders
From lod1
Group by CustomerID)

Select Total_Orders, Count(Distinct CustomerID) as Frequency
From Orders_per_Customer
Group by Total_Orders
```

## Result

<div class='tableauPlaceholder' id='viz1660421229617' style='position: relative'><noscript><a href='#'><img
                alt='Customer Order Frequency-- How many customers have made 1, 2, 3, N orders?  '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD1-CustomerOrderFrequency_16588880333730&#47;CustomerOrderFrequency&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
