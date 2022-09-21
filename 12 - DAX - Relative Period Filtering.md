# Relative Period Filtering

```DAX
DayofYear = DATEDIFF(STARTOFYEAR('DAX'[OrderDate]),'DAX'[OrderDate],DAY)+1

YTDProfit = CALCULATE(SUM('DAX'[Profit]),DATESYTD('Date'[Date]))

YTDProfit2 = CALCULATE(SUM('DAX'[Profit]), Filter('DAX','DAX'[DayofYear]<=[YTD]))

DAX-YTD = 
Var MaxDate = CALCULATE(MAX('Date'[Date]),ALL('Date'))
Var YTD_ = CALCULATE(MAX('Date'[DayofYear]),FILTER(ALL('Date'),'Date'[Date]=MaxDate))
Return
GROUPBY(
    ADDCOLUMNS(
        GROUPBY(FILTER('DAX','DAX'[DayofYear]<=YTD_),'Date'[Year],'Date'[WeekNum],'Date'[Date]),
        "YTD_Daily_Profit",[YTDProfit]),
    [Year],[WeekNum],
    "YTD_Weekly_Profit",MAXX(CURRENTGROUP(),[YTD_Daily_Profit]))

Diff = CALCULATE(SUM('DAX-YTD'[YTD_Weekly_Profit]),'DAX-YTD'[Year]=MAX('DAX-YTD'[Year])) - CALCULATE(SUM('DAX-YTD'[YTD_Weekly_Profit]),'DAX-YTD'[Year]=MIN('DAX-YTD'[Year]))

Date = CALENDAR(MIN('DAX'[OrderDate]),MAX('DAX'[OrderDate]))

DayofYear = DATEDIFF(STARTOFYEAR('Date'[Date]),'Date'[Date],DAY)+1

WeekNum = WEEKNUM('Date'[Date])

Year = YEAR('Date'[Date])

YTD = 
Var 
    MaxDate = CALCULATE(MAX('Date'[Date]),ALL())
Return
    CALCULATE(SUM('Date'[DayofYear]),FILTER(ALL('Date'),'Date'[Date] = MaxDate))
```

# Result

![DAX12](https://user-images.githubusercontent.com/79496040/191628322-46f648d9-414c-4a20-9994-ab3c5ee057e7.gif)

