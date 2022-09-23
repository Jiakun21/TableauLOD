# Actual vs. Target

```PY
import pandas as pd
import numpy as np

import panel as pn
from panel.interact import interact
pn.extension('echarts')

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

from pyecharts import options as opts
from pyecharts.charts import Line, Bar, Grid
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod08.csv', sep = '\t')

grp_Prod_State = df.groupby(['State','Product']).sum()[['Profit','TargetProfit']]
grp_Prod_State['Diff'] = grp_Prod_State['Profit'] - grp_Prod_State['TargetProfit']
grp_Prod_State['Num_Prod_Profit_GT_Target'] = grp_Prod_State['Diff'].apply(lambda x : 1 if x > 0 else 0)
grp_Prod_State = grp_Prod_State.reset_index()

grp_State = grp_Prod_State.groupby(['State']).agg({'Diff':'sum','Num_Prod_Profit_GT_Target':'sum','Product':'count'})
grp_State['Pct_Prod_Profit_GT_Target'] = (grp_State['Num_Prod_Profit_GT_Target']/grp_State['Product']).mul(100).round(1)
grp_State = grp_State.sort_values(by = 'Diff', ascending = True)[['Diff','Pct_Prod_Profit_GT_Target']]

grp_Prod = df.groupby(['Product']).sum()[['Profit','TargetProfit']]
grp_Prod = grp_Prod.sort_values(by = 'Profit', ascending = False)

State = grp_State.index.tolist()
Diff = grp_State.Diff.tolist()
Pct = grp_State.Pct_Prod_Profit_GT_Target.tolist()

Product = grp_Prod.index.tolist()
Profit = grp_Prod.Profit.tolist()
Target = grp_Prod.TargetProfit.tolist()

js_color = """function (params) {
        if (params.value > 0) {
                return '#90cee3';
            } else {
                return '#ffbb78';
            }
    }"""

b1 = (Bar()
        .add_xaxis(State)
        .add_yaxis('', 
                   Diff,
                   tooltip_opts = opts.TooltipOpts(
                            trigger = "item", 
                            formatter = "In <b>{b}</b> the difference between actual and target is <b>${c}</b>."),
                   itemstyle_opts = opts.ItemStyleOpts(color = JsCode(js_color)),
                    )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            title_opts = opts.TitleOpts(
                title = "Actual vs. Target",
                subtitle = "What percentage of products are meeting their profit target in each state?"),
            yaxis_opts = opts.AxisOpts(
                name = "Difference Between Profit and Target",
                name_location = 'start',
                name_gap = 40,
                axistick_opts = opts.AxisTickOpts(is_show = False),
                    ),
        )
        #.render_notebook()
    )
b1

b2 = (Bar()
        .add_xaxis(State)
        .add_yaxis('', 
                   Pct,
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "In <b>{b}</b> the percentage products above target is <b>{c}%</b>."),
                   itemstyle_opts = opts.ItemStyleOpts(color = '#d6d2d2'),
                    )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            yaxis_opts = opts.AxisOpts(
                is_show = False,
                name = "% of Product Above Target",
                name_location = 'start',
                name_gap = 40,
                axislabel_opts = opts.LabelOpts(formatter = "{value}%"),
                axistick_opts = opts.AxisTickOpts(is_show = False),
                    ),
        )
        #.render_notebook()
    )
b2

b3 = (Bar()
        .add_xaxis(Product)
        .add_yaxis('Profit', 
                   Profit,
                   itemstyle_opts = opts.ItemStyleOpts(
                       color = '#90cee3' ),  # Color formatting                                     
                   )
        .add_yaxis('Target', 
                   Target,
                   itemstyle_opts = opts.ItemStyleOpts(
                       color = '#ffbb78' ),  # Color formatting                                     
            )
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            yaxis_opts = opts.AxisOpts(
                name = "Profit",
                name_location = 'center',
                name_gap = 60,
                axistick_opts = opts.AxisTickOpts(is_show = False),
                    ),
        )
        #.render_notebook()
    )

grid = (
    Grid(init_opts = opts.InitOpts(width = "900px", height = "500px"))
    .add(
        b1, 
        grid_opts = opts.GridOpts(pos_right = "58%", pos_bottom = "50%"), 
        )
    .add(
        b2, 
        grid_opts = opts.GridOpts(pos_left = "58%", pos_bottom = "50%"), 
        )
    .add(
        b3, 
        grid_opts = opts.GridOpts(pos_top = "60%"), 
        )
    .render_notebook()
)

grid
```

# Result

![PY08](https://user-images.githubusercontent.com/79496040/192030251-729b8c46-deb7-4c96-8b81-2c3fb292a250.gif)

### Comments

To-Do: Build a connection between different Bar() objects; 
