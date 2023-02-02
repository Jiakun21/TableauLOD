# Percent of Total

```sql
SELECT
    Country, Market,
    Round(SUM(Sales) / (SELECT SUM(Sales) FROM Superstore) * 100) AS Revenue_Percentage
FROM lod4
GROUP BY Country, Market;
```
