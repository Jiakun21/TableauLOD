# Customer Order Frequency

Link: https://public.tableau.com/app/profile/jiakun.zheng/viz/LOD1-CustomerOrderFrequency_16588880333730/CustomerOrderFrequency

```sql
With Orders_per_Customer as (
Select CustomerID, Count(Distinct OrderID) as Total_Orders
From lod1
Group by CustomerID)

Select Total_Orders, Count(Distinct CustomerID) as Frequency
From Orders_per_Customer
Group by Total_Orders
```

## Result

<div class='tableauPlaceholder' id='viz1660421229617' style='position: relative'><noscript><a href='#'><img
                alt='Customer Order Frequency-- How many customers have made 1, 2, 3, N orders?  '
                src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD1-CustomerOrderFrequency_16588880333730&#47;CustomerOrderFrequency&#47;1_rss.png'
                style='border: none' /></a></noscript><object class='tableauViz' style='display:none;'>
        <param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' />
        <param name='embed_code_version' value='3' />
        <param name='site_root' value='' />
        <param name='name' value='LOD1-CustomerOrderFrequency_16588880333730&#47;CustomerOrderFrequency' />
        <param name='tabs' value='no' />
        <param name='toolbar' value='yes' />
        <param name='static_image'
            value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;LO&#47;LOD1-CustomerOrderFrequency_16588880333730&#47;CustomerOrderFrequency&#47;1.png' />
        <param name='animate_transition' value='yes' />
        <param name='display_static_image' value='yes' />
        <param name='display_spinner' value='yes' />
        <param name='display_overlay' value='yes' />
        <param name='display_count' value='yes' />
        <param name='language' value='en-US' />
    </object></div>
