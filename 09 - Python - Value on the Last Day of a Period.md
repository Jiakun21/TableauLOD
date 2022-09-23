# Value on the Last Day of a Period

```PY
import pandas as pd

from pyecharts import options as opts
from pyecharts.charts import Line, Grid
from pyecharts.commons.utils import JsCode

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

df = pd.read_csv('lod09.csv', sep = '\t')

df['Date'] = pd.to_datetime(df['Date'], format = '%Y-%m-%d')

grp_Ticker = df.groupby(['Ticker']).resample('M', on = 'Date')
grp_Ticker = grp_Ticker.agg({'Close':['mean','last'],'Date':'last'})

Ticker = df.Ticker.unique().tolist()

x = grp_Ticker.index.get_level_values(1).tolist()
LastDay = [grp_Ticker.loc[t]['Date']['last'].dt.strftime("%Y-%m-%d").tolist() for t in Ticker]
Avg_Price = [grp_Ticker.loc[t]['Close']['mean'].round(2).tolist() for t in Ticker]
LastDay_Price = [grp_Ticker.loc[t]['Close']['last'].tolist() for t in Ticker]

L = (
    Line()
    .add_xaxis(x)
    .add_yaxis(Ticker[0] + ", Avg. Adj Close", 
                Avg_Price[0],
                color = '#18becf',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .add_yaxis(Ticker[1] + ", Avg. Adj Close", 
                Avg_Price[1],
                color = '#ffc156',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .add_yaxis(Ticker[2] + ", Avg. Adj Close", 
                Avg_Price[2],
                color = '#ff7f0e',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .add_yaxis(Ticker[0] + ", Close value on last day", 
                LastDay_Price[0],
                color = '#9edae5',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .add_yaxis(Ticker[1] + ", Close value on last day", 
                LastDay_Price[1],
                color = '#ffdd71',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .add_yaxis(Ticker[2] + ", Close value on last day", 
                LastDay_Price[2],
                color = '#ffbb78',
                linestyle_opts = opts.LineStyleOpts(width = 2),
                label_opts = opts.LabelOpts(is_show = False), 
                )
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Value on the Last Day of a Period",
            subtitle = "What is the close value of each stock on the last day of the month compared the average daily close value?"),
        legend_opts = opts.LegendOpts(
            pos_top = "5%", 
            pos_right = "5%",
            orient = "vertical"),
        xaxis_opts = opts.AxisOpts(type_ = "time"),
        tooltip_opts=opts.TooltipOpts(
            trigger = "axis", 
                        )
        )
    .render_notebook()
)

L
```

# Result

![PY09](https://user-images.githubusercontent.com/79496040/191983785-5c8711c1-3fac-4632-8278-233aa372a7de.gif)

