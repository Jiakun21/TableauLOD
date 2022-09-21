# Average of Top Deals by Sales Rep

```PY
import pandas as pd

import panel as pn
from panel.interact import interact
pn.extension('echarts')

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

from pyecharts import options as opts
from pyecharts.charts import Map, Bar, Grid
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod07.csv', sep = ',')

MaxSales_Country_SalesRep = df.groupby(['Country','Sales Rep']).max()[['Sales']].reset_index()
Avg_Top_Deal = MaxSales_Country_SalesRep.groupby(['Country']).mean()[['Sales']].round(0).reset_index()

MaxSales_SalesRep = df.groupby(['Sales Rep']).max()[['Sales']].reset_index().round().sort_values(by = 'Sales')

data = [list(z) for z in zip(Avg_Top_Deal['Country'].tolist(), Avg_Top_Deal['Sales'].tolist())]

maxmin = Avg_Top_Deal['Sales'].sort_values(ascending = False).tolist()
maxmin[0], maxmin[-1]

js_country = """function (params) {
        console.log(params);
        return params.data.name;;
    }"""

M = (
    Map(init_opts = opts.InitOpts(width = "1000px", height = "550px"))
    .add(" ", 
         data_pair = data, 
         maptype = "world",
         is_map_symbol_show = False)
    .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Average of Top Deals by Sales Rep",
            subtitle = "What is the top deal closed by the sales rep, and the average of these deals by country?"),
        visualmap_opts = opts.VisualMapOpts(
            min_ = maxmin[-1],
            max_ = maxmin[0],
            range_text = ["High", "Low"],
            is_calculable = True,
            range_color = ["#ba684e", "#fcb76b", "#aad2e8", "#5882aa"]),
        tooltip_opts=opts.TooltipOpts(
            trigger = "item", 
            # formatter = JsCode(js_country),)
            formatter = "<b>{b}'s</b> average largest deal size is <b>${c}</b>."),
    )
    .render_notebook()
)
M

Country_ = 'Mauritania'

data2 = df.query("Country == '" + Country_ + "'").groupby(['Sales Rep']).max()[['Sales']].reset_index().round().sort_values(by = 'Sales')

SalesRep = data2['Sales Rep'].tolist()
MaxSales = data2['Sales'].tolist()
Avg_MaxSales = round(data2['Sales'].mean())

js_color = """function (params) {
        if (params.value > """ + str(Avg_MaxSales) + """) {
                return '#90cee3';
            } else {
                return '#ffbb78';
            }
    }"""

b = (Bar()
        .add_xaxis(SalesRep)
        .add_yaxis('', 
                   MaxSales,
                   itemstyle_opts = opts.ItemStyleOpts(
                       color = JsCode(js_color)   # Color formatting
                   ),
                   markline_opts = opts.MarkLineOpts(
                       data = [opts.MarkLineItem(type_ = "average", )],
                       precision = 0,
                       is_silent = True,
                       label_opts = opts.LabelOpts(is_show = False),
                       linestyle_opts = opts.LineStyleOpts(width = 3, color = '#000000', type_ = 'dash', )
                    ),)
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            title_opts = opts.TitleOpts(
                title = "Top Deal by Sales Rep in " + Country_ + " Compared to National Average",
                pos_left = "center"),
            tooltip_opts = opts.TooltipOpts(
                trigger = "item", 
                formatter = "<b>{b}'s</b> largest deal size is <b>${c}</b> compared to the average of <b>$" + str(Avg_MaxSales) + "</b>.")
        )
        .render_notebook()
    )
b
```

# Result

