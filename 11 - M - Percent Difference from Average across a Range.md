# Percent Difference from Average across a Range

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod11.csv"),[Delimiter=",", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    Dataset = Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}, {"Ticker", type text}, {"Close", type number}}),
    #"Filtered Rows" = Table.SelectRows(Dataset, each [Date] >= #"Start Reference Date" and [Date] <= #"End Reference Date"),
    CalAvg = Table.Group(#"Filtered Rows", {"Ticker"}, {{"Avg_Price", each List.Average([Close]), type nullable number}}),
    #"Merged Queries" = Table.NestedJoin(Dataset, {"Ticker"}, CalAvg, {"Ticker"}, "tbl", JoinKind.LeftOuter),
    #"Expanded tbl" = Table.ExpandTableColumn(#"Merged Queries", "tbl", {"Avg_Price"}, {"Avg_Price"}),
    #"Added Diff" = Table.AddColumn(#"Expanded tbl", "Diff", each ([Close] - [Avg_Price])/[Avg_Price]),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Diff",{{"Diff", Percentage.Type}})
in
    #"Changed Type"

let
    Source = Csv.Document(File.Contents("C:\Users\lod11.csv"),[Delimiter=",", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Date", type date}, {"Ticker", type text}, {"Close", type number}}),
    Date = #"Changed Type"[Date],
    #"Removed Duplicates" = List.Distinct(Date)
in
    #"Removed Duplicates"
```

# Result
