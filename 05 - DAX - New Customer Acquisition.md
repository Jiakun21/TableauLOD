# New Customer Acquisition

```DAX
FirstPurchase = CALCULATE(MIN('DAX'[OrderDate]), ALLEXCEPT('DAX', 'DAX'[CustomerID]))

DistCnt = DISTINCTCOUNT('DAX'[CustomerID])

DAX_ =
VAR New_Customers =
    CALCULATETABLE ( 'DAX', 'DAX'[OrderDate] = 'DAX'[FirstPurchase] )
RETURN
    SUMMARIZE ( New_Customers, [Market], [OrderDate], "DistCnt", [DistCnt] )

RunningTotal2 =
VAR MaxDate =
    CALCULATE ( MAX ( 'DAX_'[OrderDate] ), ALLSELECTED ( 'DAX_'[Market] ) )
RETURN
    CALCULATE ( SUM ( DAX_[DistCnt] ), 'DAX_'[OrderDate] <= MaxDate )
```

# Result

