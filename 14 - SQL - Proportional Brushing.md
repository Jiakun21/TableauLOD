# Proportional Brushing

```SQL
-- Sol 1:
Select 
    SubCategory,
    Sum(Sum(If(Country = 'Australia', Sales, 0))) Over() as TotalSales,
    Sum(If(Country = 'Australia', Sales, 0)) as Sales_per_Country,
    Sum(Sales) as Sales,
    Round(Sum(If(Country = 'Australia', Sales, 0))/Sum(Sales) * 100, 2) as Pct
From lod14
Group by Market, SubCategory
Order by Sales_per_Country Desc;



-- Sol 2:
Select 
    SubCategory, Country,
    Sum(Sum(Sales)) Over(Partition by Country) as Sales_per_Country,
    Sum(Sales) as Sales_per_Country_per_SubCategory,
    Sum(Sum(Sales)) Over(Partition by SubCategory) as Sales_per_SubCategory,
    Round(Sum(Sales)/Sum(Sum(Sales)) Over(Partition by SubCategory) * 100, 2) as Pct
From lod14
Group by Market, Country, SubCategory
Order by Country, Sales_per_Country_per_SubCategory Desc
```
