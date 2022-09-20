# Average of Top Deals by Sales Rep

```SQL
With Stats as (
Select 
    Country, SalesRep,
    Round(Max(Sales)) as Top_Deal,
    Round(Avg(Max(Sales)) Over(Partition by Country)) as Avg_Top_Deal
From lod7
-- Where Country Like 'Mau%'
Group by Country, SalesRep
Order by Country, Top_Deal Desc)

-- Reference Line for National Average
Select Round(Avg(Top_Deal)) as Avg_Top_Deal
From (
	Select SalesRep, Max(Top_Deal) as Top_Deal	
	From Stats
Group by SalesRep) a
```
