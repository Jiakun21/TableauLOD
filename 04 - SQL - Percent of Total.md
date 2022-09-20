# Percent of Total

```sql
Select Country, Market,
    Round(Sum(Sales)/Sum(Sum(Sales)) Over() * 100) as Percent
From lod4
Group by Country, Market
```
