# [Value on the Last Day of a Period](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD9-ValueontheLastDayofaPeriod/ValueontheLastDayofaPeriod)

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

## Result

<div class='tableauPlaceholder' id='viz1660673320476' style='position: relative'><noscript><a href='#'><img
                alt='Value on the Last Day of a Period-- What is the close value of each stock on the last day of the month compared the average daily close value? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD9-ValueontheLastDayofaPeriod&#47;ValueontheLastDayofaPeriod&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
