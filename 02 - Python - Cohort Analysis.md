# Cohort Analysis

```PY
import pandas as pd
import numpy as np

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB

import panel as pn
pn.extension('echarts')

from pyecharts import options as opts
from pyecharts.charts import Bar

df = pd.read_csv('lod02.csv', sep = '\t')
df['OrderDate'] = pd.to_datetime(df['OrderDate'], format = '%Y-%m-%d')
df['OrderYear'] = df['OrderDate'].dt.year

Cohort = df.groupby(['CustomerID'])['OrderDate'].min().dt.year.reset_index()
Cohort.columns = ['CustomerID', 'CohortYear']

df2 = pd.merge(df, Cohort, on = ['CustomerID'], how = 'left')

market = df2['Market'].unique().tolist()
Sel_Market = pn.widgets.Select(name = 'Market', options = market, width = 200)

@pn.depends(Sel_Market.param.value)
def lbar(Sel_Market):

    subset = df2[df2.Market == Sel_Market]

    cross_tab = pd.crosstab(index = subset['OrderYear'], 
                            columns = subset['CohortYear'], 
                            values = subset['Sales'], 
                            aggfunc = 'sum')

    OrderYear = cross_tab.index.tolist()
    data = [np.divide(cross_tab.iloc[:,i],1000).round(0) for i in np.arange(cross_tab.columns.size)]

    b1 = (Bar()
        .add_xaxis(cross_tab.index.tolist())
        .add_yaxis(str(OrderYear[0]), 
                    data[0].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#7ec8e1'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>${c}k</b>."),
                    )
        .add_yaxis(str(OrderYear[1]), 
                    data[1].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#418dbb'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>${c}k</b>."),
                    )
        .add_yaxis(str(OrderYear[2]), 
                    data[2].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#1c5f9e'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>${c}k</b>."),
                    )
        .add_yaxis(str(OrderYear[3]), 
                    data[3].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#26456e'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>${c}k</b>."),
                    )            
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            yaxis_opts = opts.AxisOpts(
                name = "Sales",
                #name_location = 'top',
                #name_gap = 45,
                axislabel_opts = opts.LabelOpts(formatter = "${value}k")),
            legend_opts = opts.LegendOpts(is_show = False)
            )
        # .render_notebook()
    )
      
    return pn.pane.ECharts(b1, height = 550, width = 550)

@pn.depends(Sel_Market.param.value)
def rbar(Sel_Market):

    subset = df2[df2.Market == Sel_Market]

    cross_tab_prop = pd.crosstab(index = subset['OrderYear'], 
                                 columns = subset['CohortYear'], 
                                 values = subset['Sales'], 
                                 aggfunc = 'sum',
                                 normalize = 'index')

    OrderYear = cross_tab_prop.index.tolist()
    data = [np.multiply(cross_tab_prop.iloc[:,i],100).round(2) for i in np.arange(cross_tab_prop.columns.size)]

    b2 = (Bar()
        .add_xaxis(cross_tab_prop.index.tolist())
        .add_yaxis(str(OrderYear[0]), 
                    data[0].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#7ec8e1'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>{c}%</b> of annual sales."),
                    )
        .add_yaxis(str(OrderYear[1]), 
                    data[1].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#418dbb'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>{c}%</b> of annual sales."),
                    )
        .add_yaxis(str(OrderYear[2]), 
                    data[2].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#1c5f9e'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>{c}%</b> of annual sales."),
                    )
        .add_yaxis(str(OrderYear[3]), 
                    data[3].tolist(),
                    stack = "stack1",
                    itemstyle_opts = opts.ItemStyleOpts(
                            color = '#26456e'),
                    tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b>, the <b>{a}</b> customer cohort generates <b>{c}%</b> of annual sales."),
                    )            
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            yaxis_opts = opts.AxisOpts(
                name = "% of Total Sales",
                max_ = 100,
                axislabel_opts = opts.LabelOpts(formatter = "{value}%")),
            legend_opts = opts.LegendOpts(pos_right = '10%')
            )
        # .render_notebook()
    )
     
    return pn.pane.ECharts(b2, height = 550, width = 550)

title = '### Cohort Analysis'
subtitle = 'How much revenue is contributed annually by each customer cohort?'
pn.Column(title, subtitle, Sel_Market, pn.Row(lbar, rbar)).servable()
```

# Result

![PY02](https://user-images.githubusercontent.com/79496040/192115205-3a218baf-0bcf-4127-b325-a3b694dfd146.gif)
