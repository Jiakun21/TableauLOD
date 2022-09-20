# Value on the Last Day of a Period

```SQL
Select Ticker, Date as Last_Day_in_Period, Close as Last_Price_in_Period, Avg_Price
From (
	Select *,
		Round(Avg(Close) Over(Partition by Ticker, Date_Format(Date,'%Y-%m')), 2) as Avg_Price,
		Rank() Over(Partition by Ticker, Date_Format(Date,'%Y-%m') Order by Date Desc) as rnk
	From lod9) T
Where rnk = 1


-- Sol 2

Select Distinct
    Ticker, 
    Max(Date) Over(Partition by Ticker, Date_Format(Date,'%Y-%m')) as Last_Day_in_Period,
    nth_Value(Close, 1) Over(Partition by Ticker, Date_Format(Date,'%Y-%m') Order by Date Desc) as Last_Price_in_Period,
    Round(Avg(Close) Over(Partition by Ticker, Date_Format(Date,'%Y-%m')), 2) as Avg_Price
From lod9
```
