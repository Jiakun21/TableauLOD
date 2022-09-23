# Relative Period Filtering

```PY
import pandas as pd
import numpy as np
import datetime

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

from pyecharts import options as opts
from pyecharts.charts import Line, Grid
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod12.csv', sep = ',')
df['OrderDate'] = pd.to_datetime(df['OrderDate'], format = '%Y-%m-%d')
df['Year'] = df['OrderDate'].dt.year
df['DayofYear'] = df['OrderDate'].dt.day_of_year
df['WeekNum'] = df['OrderDate'].dt.isocalendar().week

MaxDate = df['OrderDate'].max().day_of_year

grp_YearWeeknum = df[df['DayofYear'] <= MaxDate].groupby(['Year','WeekNum']).sum()[['Profit']].round(2)
grp_YearWeeknum['RunningTotal'] = grp_YearWeeknum.groupby(['Year']).cumsum().round(2)
grp_YearWeeknum = grp_YearWeeknum.reset_index()

Years = grp_YearWeeknum.Year.unique()

PreYear = grp_YearWeeknum[grp_YearWeeknum['Year'] == Years[0]]
CurYear = grp_YearWeeknum[grp_YearWeeknum['Year'] == Years[1]]

WeekNum = CurYear['WeekNum'].tolist()

Pre_RT = PreYear['RunningTotal'].tolist()
Cur_RT = CurYear['RunningTotal'].tolist()

pvt = grp_YearWeeknum.pivot(index = "WeekNum", columns = "Year", values = "RunningTotal").reset_index()
pvt.columns = ['WeekNum','PreYear','CurYear']
pvt['Diff'] = (pvt['CurYear'] - pvt['PreYear']).round(2)

js_chart1 = """function (x) {
        console.log(x);
        return 'The difference in year to date profit in <b>2014</b> vs. <b>2013</b> in <b>week ' + x.value[0] + '</b> is <b>$' + x.value[1] + '</b>. ';
    }"""
js_chart2 = """function (x) {
        console.log(x);
        return 'Year to date sales in <b>week ' + x.value[0] + '</b> of <b>' + x.seriesName + '</b> are <b>$' + x.value[1] + '</b>. ';
    }"""


# Line Chart 1
L1 = (
    Line()
    .add_xaxis(pvt.WeekNum.tolist())
    .add_yaxis("", 
               pvt.Diff.tolist(), 
               tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = JsCode(js_chart1)),
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts = opts.LineStyleOpts(color = "#42f5ec", width = 3),
               itemstyle_opts=opts.ItemStyleOpts(color = "#42f5ec"),
               areastyle_opts = opts.AreaStyleOpts(opacity = 0.5))
    .set_global_opts(
        xaxis_opts = opts.AxisOpts(
            is_show = False,
            type_ = 'value',
            axistick_opts = opts.AxisTickOpts(length = 5)),
        title_opts = opts.TitleOpts(
            title = "Relative Period Filtering",
            subtitle = "What is the year-to-date profit of this year versus last year, up to the maximum day in the data source, August 23, 2014, in Week 34?"))
    #.render_notebook()
)


# Line Chart 2
L2 = (
    Line()
    .add_xaxis(WeekNum)
    .add_yaxis(str(Years[0]), 
               Pre_RT, 
               tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = JsCode(js_chart2)),
               linestyle_opts = opts.LineStyleOpts(color = "#f5f52c", width = 3),
               itemstyle_opts=opts.ItemStyleOpts(color = "#f5f52c"),
               label_opts = opts.LabelOpts(is_show = False))
    .add_yaxis(str(Years[1]), 
               Cur_RT,
               tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = JsCode(js_chart1)),
               linestyle_opts = opts.LineStyleOpts(color = "#f59a2c", width = 3),
               itemstyle_opts=opts.ItemStyleOpts(color = "#f59a2c"), 
               label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        xaxis_opts = opts.AxisOpts(
            type_ = 'value',
            axistick_opts = opts.AxisTickOpts(length = 5),
                ),
    )
    #.render_notebook()
)


# Grid
G = (
    Grid(init_opts = opts.InitOpts(width = "1000px", height = "500px"))
    .add(L1, 
         grid_opts = opts.GridOpts(
             pos_bottom = "52%"))
    .add(L2, 
         grid_opts = opts.GridOpts(
             pos_top = "52%"))
    .render_notebook()
)
G
```

# Result

![PY12](https://user-images.githubusercontent.com/79496040/192039811-b68abd56-0fcf-454f-b9a4-b5308948d52f.gif)

