# Customer Order Frequency

```PY
import pandas as pd

from pyecharts import options as opts
from pyecharts.charts import Bar

df = pd.read_csv('lod01.csv', sep = ';')

Orders_per_Customer = df.groupby(['CustomerID'])['OrderID'].nunique().reset_index()
Orders_per_Customer.columns = ['CustomerID', 'Total_Orders']

Freq = Orders_per_Customer.groupby(['Total_Orders'])['CustomerID'].nunique().reset_index()
Freq.columns = ['Total_Orders', 'Frequency']

freq = Freq.Frequency.sort_values().tolist()

B = (
    Bar()
    .add_xaxis(Freq.Total_Orders.tolist())
    .add_yaxis(
        "",
        Freq.Frequency.tolist(),
    )
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Customer Orders Frequency",
            subtitle = "How many customers have made 1, 2, 3, N orders?"),
        tooltip_opts = opts.TooltipOpts(
                trigger = "item", 
                formatter = "<b>{c}</b> customers have made <b>{b}</b> purchases."),
        visualmap_opts = opts.VisualMapOpts(
            is_show = False,
            min_ = freq[0], max_ = freq[-1], 
            range_color = ["#b2d4db", "#79c2df", "#4a93bf", "#2d4b73"],
        ),
        xaxis_opts = opts.AxisOpts(
                name = "Number of Orders Placed",
                name_location = 'center',
                name_gap = 30,
                    ),
        yaxis_opts = opts.AxisOpts(
                name = "Number of Customers",
                name_location = 'center',
                name_gap = 60,
                    ),
            )
    .render_notebook()
)

B
```

# Result

![PY01](https://user-images.githubusercontent.com/79496040/192073066-540cfdeb-ee09-4cff-995d-bfc18be955e4.gif)
