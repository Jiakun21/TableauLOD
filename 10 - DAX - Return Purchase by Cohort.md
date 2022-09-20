# Return Purchase by Cohort

```DAX
Diff = (YEAR('DAX'[Second_Purchase])-YEAR('DAX'[First_Purchase]))*4+(QUARTER('DAX'[Second_Purchase])-QUARTER('DAX'[First_Purchase]))

DiffTxt_ = IF('DAX'[Diff]<0,"Lapsed",FORMAT('DAX'[Diff],"0"))

First_Purchase = CALCULATE(MIN('DAX'[OrderDate]),ALLEXCEPT('DAX','DAX'[CustomerID]))

Ref = RELATED(UDT[Index])

Second_Purchase = CALCULATE(MIN('DAX'[OrderDate]),ALLEXCEPT('DAX','DAX'[CustomerID]),'DAX'[OrderDate]>'DAX'[First_Purchase])

YQ = YEAR('DAX'[First_Purchase])&"-Q"&QUARTER('DAX'[First_Purchase])

let
    Source = {"Lapsed",0..99},
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Renamed Columns" = Table.RenameColumns(#"Converted to Table",{{"Column1", "List"}}),
    #"Added Index" = Table.AddIndexColumn(#"Renamed Columns", "Index", 0, 1, Int64.Type)
in
    #"Added Index"
```

# Result
