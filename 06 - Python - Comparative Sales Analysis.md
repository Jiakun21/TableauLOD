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
def plot(Sel_Category):
    
    idf = Sales_per_Category

    idf['Diff'] = idf.Sales - idf[idf.Category == Sel_Category].Sales.sum()
    idf = idf.sort_values(by = 'Diff', ascending = True)

    Category = idf.Category.tolist()
    Diff = idf.Diff.tolist()
    
    b = (Bar()
        .add_xaxis(Category)
        .add_yaxis('', 
                   Diff,
                   itemstyle_opts = opts.ItemStyleOpts(
                       color = '#5b7291'   
                   ))
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            title_opts = opts.TitleOpts(
                title = "Comparative Sales Analysis",
                subtitle = "How do the Sales of all of my Categories compare to that of a selected Category?"),
            tooltip_opts = opts.TooltipOpts(
                trigger = "item", 
                formatter = "The difference in sales between <b>{b}</b> and <b>" + Sel_Category + "</b> is <b>${c}</b>.")
        )
    )
    
    return pn.pane.ECharts(b, width = 900, height = 550)

pn.Column(Sel_Category, plot).servable()
```

# Result
