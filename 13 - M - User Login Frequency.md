# User Login Frequency

```M
let
    Source = Csv.Document(File.Contents("C:\Users\lod13.csv"),[Delimiter="#(tab)", Columns=3, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"LoginDate", type date}, {"Market", type text}, {"UserID", type text}}),
    #"Grouped MarketUserID" = Table.Group(#"Changed Type", {"Market", "UserID"}, {{"First_Login", each List.Min([LoginDate]), type nullable date}, {"Last_Login", each List.Max([LoginDate]), type nullable date}, {"Total_Logins", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Added TotalMonths" = Table.AddColumn(#"Grouped MarketUserID", "Total_Months", each (Date.Year([Last_Login])-Date.Year([First_Login]))*12 + (Date.Month([Last_Login])-Date.Month([First_Login]))),
    #"Added LoginFreq" = Table.AddColumn(#"Added TotalMonths", "Login_Freq", each [Total_Months]/[Total_Logins]),
    #"Inserted Rounding" = Table.AddColumn(#"Added LoginFreq", "Round_Login_Freq", each Number.Round([Login_Freq], 0, RoundingMode.AwayFromZero), type number),
    #"Grouped Market" = Table.Group(#"Inserted Rounding", {"Market"}, {{"Total_Users", each Table.RowCount(Table.Distinct(_)), Int64.Type}, {"Avg_Login_Freq", each List.Average([Login_Freq]), type number}}),
    #"Rounded Off" = Table.TransformColumns(#"Grouped Market",{{"Avg_Login_Freq", each Number.Round(_, 3), type number}}),
    #"Grouped MarketRoundLoginFreq" = Table.Group(#"Inserted Rounding", {"Market", "Round_Login_Freq"}, {{"Users", each Table.RowCount(Table.Distinct(_)), Int64.Type}}),
    #"Merged Queries" = Table.NestedJoin(#"Grouped MarketRoundLoginFreq", {"Market"}, #"Rounded Off", {"Market"}, "Grouped MarketRoundLoginFreq", JoinKind.LeftOuter),
    #"Expanded Grouped MarketRoundLoginFreq" = Table.ExpandTableColumn(#"Merged Queries", "Grouped MarketRoundLoginFreq", {"Total_Users", "Avg_Login_Freq"}, {"Total_Users", "Avg_Login_Freq"}),
    #"Added Pct" = Table.AddColumn(#"Expanded Grouped MarketRoundLoginFreq", "Pct", each [Users]/[Total_Users]),
    #"Removed Columns" = Table.RemoveColumns(#"Added Pct",{"Users", "Total_Users"}),
    #"Added Diff" = Table.AddColumn(#"Removed Columns", "Diff", each [Round_Login_Freq]-[Avg_Login_Freq]),
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Diff",{{"Diff", type number}, {"Pct", Percentage.Type}})
in
    #"Changed Type1"
```

# Result
