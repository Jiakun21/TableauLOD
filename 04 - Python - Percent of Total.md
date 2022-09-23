# Percent of Total

```PY
import pandas as pd
import numpy as np
from pyecharts import options as opts
from pyecharts.charts import Map

df = pd.read_csv('lod04.csv', sep = '\t')

grp_market_country = df.groupby(['Market', 'Country']).sum()[['Sales']]

grp_market_country['Percent'] = (grp_market_country['Sales']/grp_market_country['Sales'].sum()).mul(100).round(0)
grp_market_country = grp_market_country.reset_index()

data = [list(z) for z in zip(grp_market_country['Country'].tolist(), grp_market_country['Percent'].tolist())]

M = (
    Map()
    .add("", data, "world", is_map_symbol_show = False)
    .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Percent of Total",
            subtitle = "For a given market what is each country’s percent of total global sales?"),
        visualmap_opts = opts.VisualMapOpts(
            min_ = 0,
            max_ = 18,
            range_text = ["High", "Low"],
            is_calculable = True,
            range_color = ["#c9e9e0", "#a9d8d8", "#8bc7d0", "#69b4c7", "#5da0bb", "#5a8dac", "#557a9d"]),
        tooltip_opts=opts.TooltipOpts(
            trigger = "item", formatter = "<b>{b}</b> represents <b>{c}%</b> of total sales globally."),
    )
    .render_notebook()
)
M
```

# Result

![PY04](https://user-images.githubusercontent.com/79496040/191878554-c113fe3d-bbd4-4a16-a18f-32708403cc6b.gif)

### Comments

Panel might not work with Map() object.
