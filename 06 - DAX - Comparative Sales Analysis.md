# Comparative Sales Analysis

```DAX
SelCategory = SELECTEDVALUE(Category[Category],"ALL")

Diff = 
Var Sel_Category_Sales = CALCULATE(SUM('DAX'[Sales]), FILTER(ALL('DAX'[Category]),'DAX'[Category] = [SelCategory]))
Return SUM('DAX'[Sales]) - Sel_Category_Sales

Sel = 
CALCULATE(SUM('DAX'[Sales]), FILTER(ALL('DAX'[Category]),'DAX'[Category] = [SelCategory]))
```

# Result

![DAX06](https://user-images.githubusercontent.com/79496040/191834902-99744521-a82e-423a-8ec1-3d56d5de78dc.gif)
