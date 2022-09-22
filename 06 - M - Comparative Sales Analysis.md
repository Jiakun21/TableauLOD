# Comparative Sales Analysis

```M
let
    Source = Csv.Document(File.Contents("C:\Users\LOD06.csv"),[Delimiter=";", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Category", type text}, {"Sales", Int64.Type}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"Category"}, {{"TotalSales", each List.Sum([Sales]), type nullable number}}),
    #"Filtered Rows" = Table.SelectRows(#"Grouped Rows", each [Category] = #"Selected Category")[TotalSales]{0},
    #"Added Diff" = Table.AddColumn(#"Grouped Rows", "Diff", each [TotalSales] - #"Filtered Rows"),
    #"Sorted Rows" = Table.Sort(#"Added Diff",{{"Diff", Order.Descending}})
in
    #"Sorted Rows"


let
    Source = Csv.Document(File.Contents("C:\Users\LOD06.csv"),[Delimiter=";", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Category", type text}, {"Sales", Int64.Type}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"Category"}, {{"TotalSales", each List.Sum([Sales]), type nullable number}}),
    #"Get List" = #"Grouped Rows"[Category]
in
    #"Get List"


"Computer Peripherals" meta [IsParameterQuery=true, ExpressionIdentifier=Category, Type="Text", IsParameterQueryRequired=true]
```

# Result

![PQ06](https://user-images.githubusercontent.com/79496040/191836938-79c4ba7c-deb9-4f48-851b-678e9d111c01.gif)
