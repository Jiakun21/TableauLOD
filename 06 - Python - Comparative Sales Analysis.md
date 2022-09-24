# Comparative Sales Analysis

```PY
import pandas as pd

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB

import panel as pn
pn.extension('echarts')

from pyecharts import options as opts
from pyecharts.charts import Bar
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod06.csv', sep = ';')

Sales_per_Category = df.groupby(['Category']).sum()[['Sales']].reset_index()

Sel_Category = pn.widgets.Select(name = 'Select a Category', options = Sales_per_Category.Category.tolist())

@pn.depends(Sel_Category.param.value)
def lbar(Sel_Category):
    
    idf = Sales_per_Category

    idf['Diff'] = idf.Sales - idf[idf.Category == Sel_Category].Sales.sum()
    idf['Sel'] = idf.apply(lambda t : t['Sales'] if t['Category'] == Sel_Category else 0, axis = 1)
    idf = idf.sort_values(by = 'Diff', ascending = True)

    Category = idf.Category.tolist()
    Diff = idf.Diff.tolist()
    Sales = idf.Sales.tolist()
    Sel = idf.Sel.tolist()
    
    b1 = (Bar()
        .add_xaxis(Category)
        .add_yaxis('', 
                   Diff,
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "The difference in sales between <b>{b}</b> and <b>" + Sel_Category + "</b> is <b>${c}</b>."),
                   itemstyle_opts = opts.ItemStyleOpts(
                        color = '#5b7291' 
                   ))
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            title_opts = opts.TitleOpts(
                title = "Comparative Sales Analysis",
                subtitle = "How do the Sales of all of my Categories compare to that of a selected Category?"),
        )
    )
      
    return pn.pane.ECharts(b1, height = 550)

@pn.depends(Sel_Category.param.value)
def rbar(Sel_Category):
    
    idf = Sales_per_Category

    idf['Diff'] = idf.Sales - idf[idf.Category == Sel_Category].Sales.sum()
    idf['Sel'] = idf.apply(lambda t : t['Sales'] if t['Category'] == Sel_Category else 0, axis = 1)
    idf = idf.sort_values(by = 'Diff', ascending = True)

    Category = idf.Category.tolist()
    Diff = idf.Diff.tolist()
    Sales = idf.Sales.tolist()
    Sel = idf.Sel.tolist()
    
    b2 = (Bar()
        .add_xaxis(Category)
        .add_yaxis('', 
                   Sales,
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>{b}</b> Sales: <b>${c}</b>"),
                   itemstyle_opts = opts.ItemStyleOpts(
                        color = '#d5d5d5'
                   ))
        .add_yaxis('', 
                   Sel,
                   gap = "-100%",
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>{b}</b> Sales: <b>${c}</b>"),
                   itemstyle_opts = opts.ItemStyleOpts(
                        color = '#fbde72'
                   ))
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(yaxis_opts = opts.AxisOpts(is_show = False),)
    )
    
    return pn.pane.ECharts(b2, height = 550)  

pn.Column(Sel_Category, pn.Row(lbar, rbar)).servable()
```

# Result

![PY06](https://user-images.githubusercontent.com/79496040/192101717-78a47235-0a97-464c-890c-6cbe1cc091e5.gif)

### Comments

There seems to be an issue working JsCode with Panel, resulting in failure to color-format Bar() object.</br>
Y-axis label cannot fully display: tried to rotate the label and offset Y-axis.
