# Comparative Sales Analysis

Link: https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD6-ComparativeSalesAnalysis_16590190002470/ComparativeSalesAnalysis

```SQL
Select Category, Sum(Sales) - Sel_Category_Sales as Diff
From lod6, 
     (Select Sum(If(Category = 'Computer Peripherals', Sales, 0)) as Sel_Category_Sales
      From lod6) Temp
Group by Category
Order by Diff Desc
```

## Result

<div class='tableauPlaceholder' id='viz1660586665053' style='position: relative'><noscript><a href='#'><img alt=' '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD6-ComparativeSalesAnalysis_16590190002470&#47;ComparativeSalesAnalysis&#47;1_rss.png'
                style='border: none' /></a></noscript></div>
