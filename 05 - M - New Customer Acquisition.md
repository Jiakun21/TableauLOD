# New Customer Acquisition

```M
let
    Source = Csv.Document(File.Contents("C:\Users\zheng\LOD05.csv"),[Delimiter="	", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"CustomerID", type text}, {"Market", type text}, {"OrderDate", type date}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"CustomerID"}, {{"Table", each _, type table [CustomerID=nullable text, Market=nullable text, OrderDate=nullable date]}, {"FirstPurchase", each List.Min([OrderDate]), type nullable date}}),
    #"Expanded Table" = Table.ExpandTableColumn(#"Grouped Rows", "Table", {"Market", "OrderDate"}, {"Market", "OrderDate"}),
    #"Filtered Rows" = Table.SelectRows(#"Expanded Table", each [OrderDate] = [FirstPurchase]),
    #"Removed Duplicates" = Table.Distinct(#"Filtered Rows"),
    #"Grouped Market" = Table.Group(#"Removed Duplicates", {"Market"}, {{"grpMarket", each _, type table [CustomerID=nullable text, Market=nullable text, OrderDate=nullable date, FirstPurchase=nullable date]}}),
    #"Grouped MarketOrderDate" = Table.AddColumn(#"Grouped Market", "grpMarketOrderDate", each Table.Group([grpMarket], {"Market", "OrderDate"}, {{"Count", each Table.RowCount(_), Int64.Type}})),
    #"Sorted Date" = Table.AddColumn(#"Grouped MarketOrderDate", "sort", each Table.Sort([grpMarketOrderDate],{{"OrderDate", Order.Ascending}})),

//Function to Calculate Running Total
    RunTotal = (RT as table) as table =>
let
    #"Added Index" = Table.AddIndexColumn(RT, "Index", 1, 1, Int64.Type),
    #"Added RunningTotal" = Table.AddColumn(#"Added Index", "CumSum", each List.Sum(List.FirstN(#"Added Index"[Count], [Index])))
in
    #"Added RunningTotal",

//Call the Function
    RunTotals = Table.TransformColumns(#"Sorted Date", {"sort" , each RunTotal(_)}),
    #"Removed Other Columns" = Table.SelectColumns(RunTotals,{"sort"}),
    #"Expanded sort" = Table.ExpandTableColumn(#"Removed Other Columns", "sort", {"Market", "OrderDate", "Count", "CumSum"}, {"Market", "OrderDate", "Count", "CumSum"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Expanded sort",{{"Count", Int64.Type}, {"CumSum", Int64.Type}})

in
    #"Changed Type1"
```


# Result
