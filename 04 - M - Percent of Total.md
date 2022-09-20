# Percent of Total

```M
let
    Source = Csv.Document(File.Contents("C:\Users\LOD04.csv"),[Delimiter="	", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Country", type text}, {"Market", type text}, {"Sales", type number}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"Country", "Market"}, {{"Sales", each List.Sum([Sales]), type nullable number}}),
    #"Cal Pct" = Table.AddColumn(#"Grouped Rows", "Percent", each [Sales]/List.Sum(#"Grouped Rows"[Sales])),
    #"Changed Type1" = Table.TransformColumnTypes(#"Cal Pct",{{"Percent", Percentage.Type}})
in
    #"Changed Type1"
```

# Result
