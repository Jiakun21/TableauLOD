# Daily Profit KPI

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod03.csv"),[Delimiter="	", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"OrderDate", type date}, {"Profit", Int64.Type}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"OrderDate"}, {{"DailyProfit", each List.Sum([Profit]), type nullable number}}),
    #"Added Conditional Column" = Table.AddColumn(#"Grouped Rows", "Profit_KPI", each if [DailyProfit] > 2000 then "Highly Profitable" else if [DailyProfit] < 0 then "Unprofitable" else "Profitable"),
    #"Inserted Year" = Table.AddColumn(#"Added Conditional Column", "OrderYear", each Date.Year([OrderDate]), Int64.Type),
    #"Inserted MonthTxt" = Table.AddColumn(#"Inserted Year", "OrderMonth", each Date.MonthName([OrderDate]), type text),
    #"Inserted MonthNum" = Table.AddColumn(#"Inserted MonthTxt", "Month", each Date.Month([OrderDate]), Int64.Type),
    #"Grouped Rows1" = Table.Group(#"Inserted MonthNum", {"Profit_KPI", "OrderYear", "OrderMonth", "Month"}, {{"Count", each Table.RowCount(Table.Distinct(_)), Int64.Type}})
in
    #"Grouped Rows1"
 
BG = SWITCH(SELECTEDVALUE('PQ'[Profit_KPI]),"Highly Profitable","Green","UnProfitable","Red","Orange")
```

# Result

![PQ03](https://user-images.githubusercontent.com/79496040/191813382-2a5264e7-f48c-4b44-9bfb-990a12a97809.gif)
