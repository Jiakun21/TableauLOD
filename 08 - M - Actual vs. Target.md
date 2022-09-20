# Actual vs. Target

```M
let
    Source = Csv.Document(File.Contents("C:\Users\LOD08.csv"),[Delimiter="	", Columns=4, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Product", type text}, {"State", type text}, {"Profit", Int64.Type}, {"TargetProfit", Int64.Type}})
in
    #"Changed Type"

BarColor = IF(SUM('PQ'[Profit]) > SUM('PQ'[TargetProfit]), 1, -1)



let
    Source = PQ,
    #"Grouped StateProd" = Table.Group(Source, {"Product", "State"}, {{"Diff_State_Prod", each List.Sum([Profit]) - List.Sum([TargetProfit]), type nullable number}}),
    #"Added Num_Prod_GT_Profit" = Table.AddColumn(#"Grouped StateProd", "Num_Prod_above_Profit", each if [Diff_State_Prod] > 0 then 1 else 0),
    #"Grouped State" = Table.Group(#"Added Num_Prod_GT_Profit", {"State"}, {{"Num_Prod", each Table.RowCount(Table.Distinct(_)), Int64.Type}, {"Num_Prod_above_Profit", each List.Sum([Num_Prod_above_Profit]), type number}, {"Diff_State", each List.Sum([Diff_State_Prod]), type nullable number}}),
    #"Added Pct_Prod_GT_Profit" = Table.AddColumn(#"Grouped State", "Pct_Prod_above_Profit", each [Num_Prod_above_Profit]/[Num_Prod]),
    #"Sorted Rows" = Table.Sort(#"Added Pct_Prod_GT_Profit",{{"Diff_State", Order.Descending}}),
    #"Removed Columns" = Table.RemoveColumns(#"Sorted Rows",{"Num_Prod", "Num_Prod_above_Profit"})
in
    #"Removed Columns"

SelState = SELECTEDVALUE('PQ-B12'[State],"ALL")
```

# Result
