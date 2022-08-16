# [Percent Difference from Average across a Range](https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD11-PercentDifferencefromAverageacrossaRange_16593928840110/PercentDifferencefromAverageacrossaRange)

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

## Result

<div class='tableauPlaceholder' id='viz1660677393423' style='position: relative'><noscript><a href='#'><img
                alt='Percent Difference from Average across a Range-- What is the percent difference between the daily close value and the average daily close value across a selected time range? '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD11-PercentDifferencefromAverageacrossaRange_16593928840110&#47;PercentDifferencefromAverageacrossaRange&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
