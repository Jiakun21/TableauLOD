# Percent Difference from Average across a Range

```SQL
Select Ticker, Date, Close, Round((Close - Avg_Price)/Avg_Price * 100) as Pct_Diff
From lod11 l Join 
	(Select Ticker, Avg(Close) as Avg_Price
	 From lod11
	 Where Date Between '2014-02-01' and '2014-06-01'
	 Group by Ticker) T Using(Ticker)
-- Where Ticker = 'VDSI'
Order by Ticker, Date
```
