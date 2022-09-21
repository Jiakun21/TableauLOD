# Relative Period Filtering

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod12.csv"),[Delimiter=",", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"OrderDate", type date}, {"Profit", type number}}),
    #"Inserted Week of Year" = Table.AddColumn(#"Changed Type", "WeekofYear", each Date.WeekOfYear([OrderDate]), Int64.Type),
    #"Inserted Day of Year" = Table.AddColumn(#"Inserted Week of Year", "DayofYear", each Date.DayOfYear([OrderDate]), Int64.Type),
    #"Inserted Year" = Table.AddColumn(#"Inserted Day of Year", "Year", each Date.Year([OrderDate]), Int64.Type),
    MaxDateinYear = Date.DayOfYear(List.Max(#"Inserted Year"[OrderDate])),
    #"Filtered Rows" = Table.SelectRows(#"Inserted Year", each [DayofYear] <= MaxDateinYear),
    #"Grouped Year_WeekNum" = Table.Group(#"Filtered Rows", {"Year", "WeekofYear"}, {{"Profit", each List.Sum([Profit]), type nullable number}}),
    #"Grouped Year" = Table.Group(#"Grouped Year_WeekNum", {"Year"}, {{"tbl", each _, type table [Year=number, WeekofYear=number, Profit=nullable number]}}),
    //Function to Calculate Running Totals
RunTotal = ( RunTable as table) as table =>
let
	#"Added Index" = Table.AddIndexColumn(RunTable, "Index", 1, 1, Int64.Type),
	#"Added RunningTotal" = Table.AddColumn(#"Added Index", "RT", each List.Sum(List.FirstN(#"Added Index"[Profit], [Index])))
in
	#"Added RunningTotal",

//Call the Function
    RunTotals = Table.TransformColumns(#"Grouped Year", {"tbl", each RunTotal(_)}),
    #"Removed Other Columns" = Table.SelectColumns(RunTotals,{"tbl"}),
    #"Expanded tbl" = Table.ExpandTableColumn(#"Removed Other Columns", "tbl", {"Year", "WeekofYear", "Profit", "RT"}, {"Year", "WeekofYear", "Profit", "RT"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Expanded tbl",{{"Year", type text}, {"WeekofYear", Int64.Type}, {"Profit", Currency.Type}, {"RT", Currency.Type}}),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type1",{{"WeekofYear", "WeekofYear"}})

in
    #"Renamed Columns"



let
    Source = #"PQ-P1",
    #"Removed Columns" = Table.RemoveColumns(Source,{"Profit"}),
    #"Pivoted Column" = Table.Pivot(#"Removed Columns", List.Distinct(#"Removed Columns"[Year]), "Year", "RT", List.Sum),
    CurrentYear = List.Distinct(#"Removed Columns"[Year]){1},
    PreviousYear = List.Distinct(#"Removed Columns"[Year]){0},
    #"Added Diff" = Table.AddColumn(#"Pivoted Column", "Diff", each Record.Field(_, CurrentYear) - Record.Field(_, PreviousYear)),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Diff",{{"Diff", Currency.Type}})
in
    #"Changed Type"
```

# Result
