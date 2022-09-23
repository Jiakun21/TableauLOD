# New Customer Acquisition

```PY
import pandas as pd
import numpy as np

from pyecharts import options as opts
from pyecharts.charts import Line
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod05.csv', sep = '\t')

first_purchase = df.groupby(['CustomerID']).min()[['OrderDate']].reset_index()
first_purchase.columns = ['CustomerID', 'FirstPurchase']

df2 = pd.merge(df, first_purchase, on = ['CustomerID'], how = 'left')
new_customers = df2[df2['OrderDate'] == df2['FirstPurchase']]

df3 = new_customers.groupby(['Market', 'OrderDate']).nunique()[['CustomerID']].reset_index()
df3.columns = ['Market', 'OrderDate', 'DistCnt']

df3['CumSum'] = df3.groupby(['Market']).cumsum()[['DistCnt']]

market = df3['Market'].unique().tolist()

x_APAC = df3.query("Market == 'APAC'").OrderDate.tolist()
y_APAC = df3.query("Market == 'APAC'").CumSum.tolist()

x_Africa = df3.query("Market == 'Africa'").OrderDate.tolist()
y_Africa = df3.query("Market == 'Africa'").CumSum.tolist()

x_EMEA = df3.query("Market == 'EMEA'").OrderDate.tolist()
y_EMEA = df3.query("Market == 'EMEA'").CumSum.tolist()

x_EU = df3.query("Market == 'EU'").OrderDate.tolist()
y_EU = df3.query("Market == 'EU'").CumSum.tolist()

x_LATAM = df3.query("Market == 'LATAM'").OrderDate.tolist()
y_LATAM = df3.query("Market == 'LATAM'").CumSum.tolist()

x_NA = df3.query("Market == 'North America'").OrderDate.tolist()
y_NA = df3.query("Market == 'North America'").CumSum.tolist()

js_formatter = """function (x) {
        console.log(x);
        let txt = '';
        let p_date = x[0].data[0];
        for (let i = 0; i < x.length; i++) {
            txt += x[i].marker +'<b>' + x[i].seriesName +'</b> had acquired a total of <b>' + x[i].data[1] + '</b> customers. <br>';
        }
        return 'On <b>' + p_date + '</b>, <br>' + txt;

    }"""

L = (
    Line(init_opts=opts.InitOpts(width = "1200px", height = "600px"))
    .set_global_opts(
        xaxis_opts=opts.AxisOpts(type_ = "time"),
        yaxis_opts=opts.AxisOpts(
            type_ = "value",
            axistick_opts=opts.AxisTickOpts(is_show = True),
        ),
    )
    .add_xaxis(x_APAC)
    .add_yaxis(market[0], y_APAC, 
              is_smooth = True,
              symbol = "emptyCircle",
              is_symbol_show = False,
              label_opts = opts.LabelOpts(is_show = False),
              linestyle_opts=opts.LineStyleOpts(width = 2),)
    .add_xaxis(x_Africa)
    .add_yaxis(market[1], y_Africa,
               is_smooth = True,
               symbol = "emptyCircle",
               is_symbol_show = False,
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts=opts.LineStyleOpts(width = 2),)
    .add_xaxis(x_EMEA)
    .add_yaxis(market[2], y_EMEA,
               is_smooth = True,
               symbol = "emptyCircle",
               is_symbol_show = False,
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts=opts.LineStyleOpts(width = 2),)
    .add_xaxis(x_EU)
    .add_yaxis(market[3], y_EU,
               is_smooth = True,
               symbol = "emptyCircle",
               is_symbol_show = False,
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts=opts.LineStyleOpts(width = 2),)
    .add_xaxis(x_LATAM)
    .add_yaxis(market[4], y_LATAM,
               is_smooth = True,
               symbol = "emptyCircle",
               is_symbol_show = False,
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts=opts.LineStyleOpts(width = 2),)
    .add_xaxis(x_NA)
    .add_yaxis(market[5], y_NA,
               is_smooth = True,
               symbol = "emptyCircle",
               is_symbol_show = False,
               label_opts = opts.LabelOpts(is_show = False),
               linestyle_opts=opts.LineStyleOpts(width = 2),)
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "New Customer Acquisition",
            subtitle = "What is the total number of customers we`ve acquired per market by day?"),
        tooltip_opts = opts.TooltipOpts(
            trigger = "axis", 
            formatter = JsCode(js_formatter)
            ),)
    .render_notebook()
)

L
```

# Result

![PY05](https://user-images.githubusercontent.com/79496040/191886817-c93ed4e5-118a-4f07-90d9-e332d65b48a3.gif)

