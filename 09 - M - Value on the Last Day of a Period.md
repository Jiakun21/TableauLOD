# Value on the Last Day of a Period

```M
let
    Source = Csv.Document(File.Contents("C:\Users\LOD09.csv"),[Delimiter="	", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}, {"Ticker", type text}, {"Close", type number}}),
    #"Inserted Year" = Table.AddColumn(#"Changed Type", "Year", each Date.Year([Date]), Int64.Type),
    #"Inserted Month" = Table.AddColumn(#"Inserted Year", "Month", each Date.Month([Date]), Int64.Type),
    #"Grouped Rows" = Table.Group(#"Inserted Month", {"Ticker", "Year", "Month"}, {{"Avg_Price", each List.Average([Close]), type nullable number}, {"tbl", each _, type table [Date=nullable date, Ticker=nullable text, Close=nullable number, Year=number, Month=number]}}),
    #"Sorted Date" = Table.AddColumn(#"Grouped Rows", "sort", each Table.Sort([tbl],{{"Date", Order.Descending}})),
    #"Added Index" = Table.AddIndexColumn(#"Sorted Date", "Index", 0, 1, Int64.Type),
    #"Extracted Date" = Table.AddColumn(#"Added Index", "Date", each #"Sorted Date"{[Index]}[sort][Date]{0}),
    #"Added Custom" = Table.AddColumn(#"Extracted Date", "Close", each #"Sorted Date"{[Index]}[sort][Close]{0}),
    #"Removed Other Columns" = Table.SelectColumns(#"Added Custom",{"Ticker", "Avg_Price", "Date", "Close"}),
    #"Rounded Off" = Table.TransformColumns(#"Removed Other Columns",{{"Avg_Price", each Number.Round(_, 2), type number}})
in
    #"Rounded Off"

BG = SWITCH(SELECTEDVALUE('PQ'[Ticker]),"FIRE","#ff7f0e","SYMC","#ffc156","#17becf")
```

# Result

![PQ09](https://user-images.githubusercontent.com/79496040/191635704-71d08233-fbea-46ec-b1bc-0eb6def674e2.gif)
