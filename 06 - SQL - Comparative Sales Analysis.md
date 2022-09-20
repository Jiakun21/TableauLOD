# Comparative Sales Analysis

```SQL
Select Category, Sum(Sales) - Sel_Category_Sales as Diff
From lod6, 
     (Select Sum(If(Category = 'Computer Peripherals', Sales, 0)) as Sel_Category_Sales
      From lod6) Temp
Group by Category
Order by Diff Desc
```
