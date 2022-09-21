# Proportional Brushing

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod14.csv"),[Delimiter=",", Columns=4, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Country", type text}, {"Market", type text}, {"Sub-Category", type text}, {"Sales", Int64.Type}}),
    #"Grouped SubCat" = Table.Group(#"Changed Type", {"Market", "Sub-Category"}, {{"Sales_SubCat", each List.Sum([Sales]), type nullable number}}),
    #"Grouped CountrySubCat" = Table.Group(#"Changed Type", {"Country", "Market", "Sub-Category"}, {{"Sales_Country_SubCat", each List.Sum([Sales]), type nullable number}}),
    #"Merged Queries" = Table.NestedJoin(#"Grouped CountrySubCat", {"Market", "Sub-Category"}, #"Grouped SubCat", {"Market", "Sub-Category"}, "Grouped CountrySubCat", JoinKind.LeftOuter),
    #"Expanded Grouped CountrySubCat" = Table.ExpandTableColumn(#"Merged Queries", "Grouped CountrySubCat", {"Sales_SubCat"}, {"Sales_SubCat"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Grouped CountrySubCat", "Pct", each [Sales_Country_SubCat]/[Sales_SubCat]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Pct", Percentage.Type}})
in
    #"Changed Type1"

SelCountry = SELECTEDVALUE(PQ[Country],"NONE")
```

# Result

![PQ14](https://user-images.githubusercontent.com/79496040/191626196-047f92e4-363f-49c5-bff0-2a7c630d9f27.gif)
