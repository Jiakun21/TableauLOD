# Daily Profit KPI

```PY
import pandas as pd
import numpy as np
from datetime import datetime

from pyecharts import options as opts
from pyecharts.charts import Line, Grid
from pyecharts.commons.utils import JsCode


df = pd.read_csv('lod03.csv', sep = '\t')

Daily_Profit = df.groupby(['OrderDate']).sum()[['Profit']].reset_index()

def assign_KPI(x):
    if x > 2000:
        result = "Highly Profitable"
    elif x < 0:
        result = "Unprofitable"
    else:
        result = "Profitable"
    return result

Daily_Profit['Profit_KPI'] = Daily_Profit.Profit.apply(assign_KPI)

Daily_Profit['OrderDate'] = pd.to_datetime(Daily_Profit['OrderDate'], format = '%Y-%m-%d')
grp_resample = Daily_Profit.set_index('OrderDate').groupby(['Profit_KPI']).resample('M','OrderDate')

hx = pd.DatetimeIndex(grp_resample.size().loc['Highly Profitable'].index).strftime("%Y-%B").tolist()
hy = grp_resample.size().loc["Highly Profitable"].tolist()

px = pd.DatetimeIndex(grp_resample.size().loc['Profitable'].index).strftime("%Y-%B").tolist()
py = grp_resample.size().loc["Profitable"].tolist()

ux = pd.DatetimeIndex(grp_resample.size().loc['Unprofitable'].index).strftime("%Y-%B").tolist()
uy = grp_resample.size().loc["Unprofitable"].tolist()

js_formatter = """function (params) {
        console.log(params);
        return params.marker + 'In <b>' + params.value[0] + '</b> has <b>' + params.value[1] + ' ' + params.seriesName + '</b> days.' ;
    }"""


L1 = (
    Line()
    .add_xaxis(hx)
    .add_yaxis("Highly Profitable", 
                hy,
                color = '#4e79a7',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                areastyle_opts=opts.AreaStyleOpts(opacity=0.5))
    .set_global_opts(
        xaxis_opts = opts.AxisOpts(is_show = False),
        title_opts = opts.TitleOpts(
            title = "Daily Profit KPI",
            subtitle = "How many days each month are highly profitable, profitable, or unprofitable?"),
        legend_opts = opts.LegendOpts(pos_left = "30%"),
        tooltip_opts=opts.TooltipOpts(
            trigger = "item", formatter = JsCode(js_formatter))
        )
)

L2 = (
    Line()
    .add_xaxis(px)
    .add_yaxis("Profitable", 
                py,
                color = '#f39437',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                areastyle_opts=opts.AreaStyleOpts(opacity=0.5))
    .set_global_opts(
        xaxis_opts = opts.AxisOpts(is_show = False),
        legend_opts = opts.LegendOpts(pos_left = "46%"))
)

L3 = (
    Line()
    .add_xaxis(ux)
    .add_yaxis("Unprofitable", 
                uy,
                color = '#e15759',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                areastyle_opts=opts.AreaStyleOpts(opacity=0.5))
    .set_global_opts(
        legend_opts=opts.LegendOpts(pos_left = "58%"),
        )
)

(
    Grid(init_opts = opts.InitOpts(width = "1000px", height = "550px"))
    .add(
        chart = L1, 
        grid_opts = opts.GridOpts(pos_left = 50, pos_right = 50, height = "20%"))
    .add(
        chart = L2,
        grid_opts = opts.GridOpts(pos_left = 50, pos_right = 50, pos_top = "35%", height = "20%"))
    .add(
        chart = L3,
        grid_opts = opts.GridOpts(pos_left = 50, pos_right = 50, pos_top = "62%", height = "20%"))
    .render_notebook()
)
```

# Result

![PY03](https://user-images.githubusercontent.com/79496040/191885729-90c6e3f6-c460-400e-a048-8750dda33a70.gif)

