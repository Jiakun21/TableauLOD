# Percent Difference from Average across a Range

```PY
import pandas as pd
import numpy as np
import datetime as dt

import panel as pn
pn.extension('echarts')

from pyecharts import options as opts
from pyecharts.charts import Line, Grid
from pyecharts.commons.utils import JsCode

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB

df = pd.read_csv('lod11.csv', sep = ',')
df['Date'] = pd.to_datetime(df['Date'], format = '%Y-%m-%d')

Ticker = df.Ticker.unique().tolist()

Sel_Ticker = pn.widgets.Select(name = 'Ticker', options = Ticker)

Date_Range_Slider = pn.widgets.DateRangeSlider(
    name = 'Reference Date Range Slider',
    start = df['Date'].min().to_pydatetime(), end = df['Date'].max().to_pydatetime(),
    value = (dt.datetime(2014, 2, 1), dt.datetime(2014, 6, 1)),
)

start, end = Date_Range_Slider.value[0].isoformat(), Date_Range_Slider.value[1].isoformat()

sub_df = df[(df['Date'] >= start) & (df['Date'] <= end)]
Avg_Price = sub_df.groupby(['Ticker']).mean()[['Close']].reset_index()

Avg_Price_Ref_Period = np.float64(Avg_Price[Avg_Price['Ticker'] == 'FIRE'].Close.round(2))
df['Avg_Price'] = Avg_Price_Ref_Period
df['Pct_Diff'] = ((df['Close'] - df['Avg_Price'])/df['Avg_Price']).mul(100).round(0)

@pn.depends(Sel_Ticker.param.value, Date_Range_Slider.param.value)
def ChartOne(Sel_Ticker, Date_Range_Slider):
    
    start, end = Date_Range_Slider[0].isoformat(), Date_Range_Slider[1].isoformat()
    sub_df = df[(df['Date'] >= start) & (df['Date'] <= end)]
    Avg_Price = sub_df.groupby(['Ticker']).mean()[['Close']].reset_index()
    Avg_Price_Ref_Period = np.float64(Avg_Price[Avg_Price['Ticker'] == Sel_Ticker].Close.round(2))
    df['Avg_Price'] = Avg_Price_Ref_Period
    df['Pct_Diff'] = ((df['Close'] - df['Avg_Price'])/df['Avg_Price']).mul(100).round(0)
    result = df[df['Ticker'] == Sel_Ticker]
    
    # Line Chart 1
    L1 = (
        Line()
        .add_xaxis(result.Date.dt.strftime("%Y-%m-%d").tolist())
        .add_yaxis("", 
                   result.Pct_Diff.tolist(), 
                   label_opts = opts.LabelOpts(is_show = False),
                   linestyle_opts = opts.LineStyleOpts(color = "#17becf", width = 3),
                   itemstyle_opts=opts.ItemStyleOpts(color = "#17becf"),
                   areastyle_opts = opts.AreaStyleOpts(color = "#b4e3eb", opacity = 0.5))
        .set_global_opts(
            xaxis_opts = opts.AxisOpts(is_show = False),
            yaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "{value} %")),
            title_opts = opts.TitleOpts(
                title = "Percent Difference from Average across a Range",
                subtitle = "What is the percent difference between the daily close value and the average daily close value across a selected time range?"))
        .set_series_opts(
            markarea_opts = opts.MarkAreaOpts(
                data = [
                    opts.MarkAreaItem(name = "", x = (start[:10], end[:10])),
                ]
            )
        )
        #.render_notebook()
    )
    
    return pn.pane.ECharts(L1, width = 950, height = 300)

@pn.depends(Sel_Ticker.param.value, Date_Range_Slider.param.value)
def ChartTwo(Sel_Ticker, Date_Range_Slider):
    
    start, end = Date_Range_Slider[0].isoformat(), Date_Range_Slider[1].isoformat()
    sub_df = df[(df['Date'] >= start) & (df['Date'] <= end)]
    Avg_Price = sub_df.groupby(['Ticker']).mean()[['Close']].reset_index()
    Avg_Price_Ref_Period = np.float64(Avg_Price[Avg_Price['Ticker'] == Sel_Ticker].Close.round(2))
    df['Avg_Price'] = Avg_Price_Ref_Period
    df['Pct_Diff'] = ((df['Close'] - df['Avg_Price'])/df['Avg_Price']).mul(100).round(0)
    result = df[df['Ticker'] == Sel_Ticker]

    # Line Chart 2
    L2 = (
        Line()
        .add_xaxis(result.Date.dt.strftime("%Y-%m-%d").tolist())
        .add_yaxis("", 
                   result.Close.tolist(), 
                   label_opts = opts.LabelOpts(is_show = False),
                   areastyle_opts = opts.AreaStyleOpts(opacity = 0.5))
        .set_series_opts(
            markarea_opts = opts.MarkAreaOpts(
                data = [
                    opts.MarkAreaItem(name = "", x = (start[:10], end[:10])),
                        ]),
            markline_opts = opts.MarkLineOpts(
                data = [opts.MarkLineItem(y = Avg_Price_Ref_Period)]
            ),
        )
        #.render_notebook()
    )
    
    return pn.pane.ECharts(L2, width = 950, height = 300)

pn.Column(pn.Row(Sel_Ticker, Date_Range_Slider), ChartOne, ChartTwo).servable()
```

# Result

