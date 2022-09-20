# Average of Top Deals by Sales Rep

```M
let
    Source = Csv.Document(File.Contents("C:\Users\LOD07.csv"),[Delimiter=",", Columns=3, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Country", type text}, {"Sales Rep", type text}, {"Sales", type number}}),
    #"Grouped Rows" = Table.Group(#"Changed Type", {"Country", "Sales Rep"}, {{"Max_Deal", each List.Max([Sales]), type nullable number}})
in
    #"Grouped Rows"

Avg_ = 
IF( HASONEFILTER('PQ P2'[Country]),
    CALCULATE(AVERAGEX(SUMMARIZE('PQ',PQ[Sales Rep],"@Top_Sale", Max('PQ'[Max_Deal])),[@Top_Sale]),ALL('PQ'),VALUES('PQ P2'[Country])),
    CALCULATE(AVERAGEX(SUMMARIZE('PQ',PQ[Sales Rep],"@Top_Sale", Max('PQ'[Max_Deal])),[@Top_Sale]),ALL()))

BarColor = IF(MAX('PQ'[Max_Deal])>[Avg_],1,-1)

CalMaxDeal = MAX(PQ[Max_Deal])

Title = "Top Deal by Sales Rep in " & IF(HASONEVALUE('PQ P2'[Country]),SELECTEDVALUE('PQ P2'[Country]),"All") & " Compared to National Average"

  
let
    Source = PQ,
    #"Grouped Rows" = Table.Group(Source, {"Country"}, {{"Avg_Top_Deal", each List.Average([Max_Deal]), type nullable number}})
in
    #"Grouped Rows"
```

# Result
