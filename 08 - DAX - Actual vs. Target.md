# Actual vs. Target

```DAX
BarColor_ = IF(SUM('DAX'[Profit])>SUM('DAX'[TargetProfit]),1,-1)

Diff_State = SUM('DAX'[Profit])-SUM('DAX'[TargetProfit])

Num_Prod_Profit_GT_Target = 
Var grp_Prod_State = SUMMARIZE('DAX','DAX'[Product],'DAX'[State],
                               "Cnt", IF(SUM('DAX'[Profit])>SUM('DAX'[TargetProfit]),1,0))
Return SUMX(grp_Prod_State,[Cnt])

Pct_Prod_Profit_GT_Target = DIVIDE([Num_Prod_Profit_GT_Target],DISTINCTCOUNT('DAX'[Product]))

SelState_ = SELECTEDVALUE('DAX'[State],"ALL")

DAX-B2 = 
GROUPBY(
        ADDCOLUMNS(
            Summarize('DAX','DAX'[Product],'DAX'[State]),
            "Cnt", IF(CALCULATE(SUM('DAX'[Profit]))>CALCULATE(SUM('DAX'[TargetProfit])),1,0),
            "Diff", CALCULATE(SUM('DAX'[Profit]))-CALCULATE(SUM('DAX'[TargetProfit]))),
        'DAX'[State],
        "Diff_State",SUMX(CURRENTGROUP(),[Diff]),
        "Num_Prod_Profit_GT_Target",SUMX(CURRENTGROUP(),[Cnt]),
        "Num_Prod",COUNTX(CURRENTGROUP(),1))

Pct_Prod_Profit_GT_Target = 'DAX-B2'[Num_Prod_Profit_GT_Target]/'DAX-B2'[Num_Prod]
```

# Result
