# Return Purchase by Cohort

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod10.csv"),[Delimiter=",", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"CustomerID", type text}, {"OrderDate", type date}}),
    #"Grouped CustomerID" = Table.Group(#"Changed Type", {"CustomerID"}, {{"First_Purchase", each List.Min([OrderDate]), type nullable date}, {"Second_Purchase", each List.Distinct(_[OrderDate]){1}}}),
    #"Replaced Errors" = Table.ReplaceErrorValues(#"Grouped CustomerID", {{"Second_Purchase", null}}),
    #"Added YQ" = Table.AddColumn(#"Replaced Errors", "Year_Quarter", each Text.From(Date.Year([First_Purchase])) & "-Q" & Text.From(Date.QuarterOfYear([First_Purchase]))),
    #"Added Freq" = Table.AddColumn(#"Added YQ", "Diff", each (Date.Year([Second_Purchase]) - Date.Year([First_Purchase])) * 4 + (Date.QuarterOfYear([Second_Purchase]) - Date.QuarterOfYear([First_Purchase]))),
    #"Grouped YQ_Freq" = Table.Group(#"Added Freq", {"Year_Quarter", "Diff"}, {{"DistCnt", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Grouped YQ_Freq",{{"Diff", Int64.Type}}),
    #"Added Custom" = Table.AddColumn(#"Changed Type1", "DiffTxt", each if [Diff] is null then "Lapsed" else [Diff])
in
    #"Added Custom"
```

# Result
