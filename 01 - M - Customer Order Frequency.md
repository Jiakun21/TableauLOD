# Customer Order Frequency

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod01.csv"),[Delimiter=";", Columns=2, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"CustomerID", Int64.Type}, {"OrderID", Int64.Type}}),
    #"Grouped CustomerID" = Table.Group(#"Changed Type", {"CustomerID"}, {{"Total_Orders", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Grouped Total_Orders" = Table.Group(#"Grouped CustomerID", {"Total_Orders"}, {{"Frequency", each Table.RowCount(Table.Distinct(_)), Int64.Type}})
in
    #"Grouped Total_Orders"
```

# Result

![PQ01](https://user-images.githubusercontent.com/79496040/191596260-041c9010-6f89-42f6-9fdd-734660c8bb7f.gif)
