# Proportional Brushing

```PY
import pandas as pd
import numpy as np

import panel as pn
from panel.interact import interact
pn.extension('echarts')

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

from pyecharts import options as opts
from pyecharts.charts import Map, Bar, Grid, Geo
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod14.csv', sep = ',')

grp_Market_Country_SubCat = df.groupby(['Market','Country','Sub-Category']).sum()[['Sales']].reset_index()

grp_Country = df.groupby(['Country']).sum()[['Sales']].reset_index()

grp_Market_SubCat = df.groupby(['Market','Sub-Category']).sum()[['Sales']]
grp_Market_SubCat.columns = ['TotalSales_SubCat']
grp_Market_SubCat = grp_Market_SubCat.reset_index()

mrg_ = pd.merge(grp_Market_Country_SubCat, grp_Market_SubCat, on = ['Market','Sub-Category'], how = 'left')
mrg_['Pct'] = mrg_['Sales']/mrg_['TotalSales_SubCat']*100

data = [list(z) for z in zip(grp_Country['Country'].tolist(), grp_Country['Sales'].tolist())]

maxmin = grp_Country['Sales'].sort_values(ascending = False).tolist()
maxmin[0], maxmin[-1]

M = (
    Map()
    .add("", 
         data_pair = data, 
         maptype = "world",
         is_map_symbol_show = False)
    .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Proportional Brushing",
            subtitle = "How much does each country contribute to total sales by product?"),
        visualmap_opts = opts.VisualMapOpts(
            min_ = maxmin[-1],
            max_ = maxmin[0],
            range_text = ["High", "Low"],
            is_calculable = True,
            range_color = ["#ba684e", "#fcb76b", "#aad2e8", "#5882aa"]),
        tooltip_opts=opts.TooltipOpts(
            trigger = "item", 
            # formatter = JsCode(js_country),)
            formatter = "Country: <b>{b}</b> </br>Sales: <b>${c}</b>"),
    )
    .render_notebook()
)
M

Country_ = "China"

filtered = mrg_[mrg_['Country'] == Country_]
filtered = filtered.sort_values(by = 'TotalSales_SubCat')
sub_cat = filtered['Sub-Category'].tolist()
sales = filtered['Sales'].tolist()
totalsales = filtered['TotalSales_SubCat'].tolist()
pct = filtered['Pct'].round(2).tolist()
market = filtered['Market'].tolist()[0]

b1 = (Bar(init_opts = opts.InitOpts(width = "450px", height = "350px"))
        .add_xaxis(sub_cat)
        .add_yaxis('', 
                   totalsales,
                   itemstyle_opts = opts.ItemStyleOpts(color = "#9fe5ef"),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "The total sales for <b>{b}</b> in <b>" + market + "</b> is <b>${c}</b>.")
                   )
        .add_yaxis('', 
                   sales,
                   gap = "-100%",
                   itemstyle_opts = opts.ItemStyleOpts(color = "#39b0c2"),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>" + Country_ + "</b> contributed <b>${c}</b> to the total <b>{b}</b> sales in <b>" + market + "</b>.")
                   )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        #.render_notebook()
    )
b1

b2 = (Bar(init_opts = opts.InitOpts(width = "450px", height = "350px"))
        .add_xaxis(sub_cat)
        .add_yaxis('', 
                   pct,
                   itemstyle_opts = opts.ItemStyleOpts(color = "#dcddde" ),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>" + Country_ + "</b> generates <b>{c}%</b> of total <b>{b}</b> sales in <b>" + market + "</b>.") 
                   )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            xaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "{value} %")),
            yaxis_opts = opts.AxisOpts(is_show = False),
            )
        #.render_notebook()
    )
b2

grid = (
    Grid()
    .add(
        b1, 
        grid_opts = opts.GridOpts(pos_top = "10%", pos_right = "52%"), 
        )
    .add(
        b2, 
        grid_opts = opts.GridOpts(pos_top = "10%", pos_left = "52%"), 
        )
    .render_notebook()
)

grid
```

# Result

![PY14A](https://user-images.githubusercontent.com/79496040/192023840-a297e483-fd3c-4939-9220-70be9d7a4b10.gif)

![PY14B](https://user-images.githubusercontent.com/79496040/192024981-19c07602-2f71-4ecd-9ea7-6c909153ca40.gif)

### Comments

To-Do: Build a connection between Map() object and Bar() objects
