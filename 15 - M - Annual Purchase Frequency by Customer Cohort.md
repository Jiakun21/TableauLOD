# Annual Purchase Frequency by Customer Cohort

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod15.csv"),[Delimiter="#(tab)", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"OrderDate", type date}}),
    CustomerCohort = Table.Group(#"Changed Type", {"CustomerID"}, {{"CohortYear", each Date.Year(List.Min([OrderDate])), type nullable date}}),
    #"Extracted Year" = Table.TransformColumns(#"Changed Type",{{"OrderDate", Date.Year, Int64.Type}}),
    #"Renamed Columns" = Table.RenameColumns(#"Extracted Year",{{"OrderDate", "OrderYear"}}),
    AnnualPurchaseFreq = Table.Group(#"Renamed Columns", {"CustomerID", "OrderYear"}, {{"Freq", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Merged Queries" = Table.NestedJoin(AnnualPurchaseFreq, {"CustomerID"}, CustomerCohort, {"CustomerID"}, "AnnualPurchaseFreq", JoinKind.LeftOuter),
    #"Expanded AnnualPurchaseFreq" = Table.ExpandTableColumn(#"Merged Queries", "AnnualPurchaseFreq", {"CohortYear"}, {"CohortYear"}),
    NumCustpOrderYearpCohortpFreq = Table.Group(#"Expanded AnnualPurchaseFreq", {"OrderYear", "Freq", "CohortYear"}, {{"Cnt", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Grouped OrderYearCohortYear" = Table.Group(NumCustpOrderYearpCohortpFreq, {"OrderYear", "CohortYear"}, {{"tbl", each _, type table [OrderYear=number, Freq=number, CohortYear=nullable date, Cnt=number]}}),
    SortFreq = Table.AddColumn(#"Grouped OrderYearCohortYear", "sort", each Table.Sort([tbl],{{"Freq", Order.Descending}})),

    //Function to calculate running totals
    RunTotal = (RunTable as table) as table =>
let
    #"Added Index" = Table.AddIndexColumn(RunTable, "Index", 1, 1, Int64.Type),
    #"Cal RT" = Table.AddColumn(#"Added Index", "RT", each List.Sum(List.FirstN(#"Added Index"[Cnt], [Index])))
in
    #"Cal RT",

    //Call the function
    RunTotals = Table.TransformColumns(SortFreq,{"sort" , each RunTotal(_)}),
    #"Removed Other Columns" = Table.SelectColumns(RunTotals,{"sort"}),
    #"Expanded sort" = Table.ExpandTableColumn(#"Removed Other Columns", "sort", {"OrderYear", "Freq", "CohortYear", "Cnt", "Index", "RT"}, {"OrderYear", "Freq", "CohortYear", "Cnt", "Index", "RT"}),
    #"Grouped CohortYear" = Table.Group(CustomerCohort, {"CohortYear"}, {{"DistCnt", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Merged Queries1" = Table.NestedJoin(#"Expanded sort", {"CohortYear"}, #"Grouped CohortYear", {"CohortYear"}, "Grouped CohortYear", JoinKind.LeftOuter),
    #"Expanded Grouped CohortYear" = Table.ExpandTableColumn(#"Merged Queries1", "Grouped CohortYear", {"DistCnt"}, {"DistCnt"}),
    #"Added Pct" = Table.AddColumn(#"Expanded Grouped CohortYear", "Pct", each [RT]/[DistCnt]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Pct",{{"Pct", Percentage.Type}})
in
	#"Changed Type1"
```

# Result
