# Cohort Analysis

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod02.csv"),[Delimiter="	", Columns=4, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"CustomerID", type text}, {"Market", type text}, {"OrderDate", type datetime}, {"Sales", Int64.Type}}),
    #"Updated Date" = Table.TransformColumnTypes(#"Changed Type",{{"OrderDate", type date}}),
    #"Grouped Rows" = Table.Group(#"Updated Date", {"CustomerID"}, {{"Table", each _, type table [CustomerID=nullable text, Market=nullable text, OrderDate=nullable date, Sales=nullable number]}, {"Cohort", each List.Min([OrderDate]), type nullable date}}),
    #"Extracted Year" = Table.TransformColumns(#"Grouped Rows",{{"Cohort", Date.Year, Int64.Type}}),
    #"Expanded Table" = Table.ExpandTableColumn(#"Extracted Year", "Table", {"Market", "OrderDate", "Sales"}, {"Market", "OrderDate", "Sales"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Table", "OrderYear", each Date.Year([OrderDate])),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Cohort", type text}, {"OrderYear", type text}})
in
    #"Changed Type1"
```

# Result

![PQ02](https://user-images.githubusercontent.com/79496040/191819946-2616693c-1e0d-44b5-826e-6748f8a39c28.gif)
