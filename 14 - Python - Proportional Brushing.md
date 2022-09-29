# Proportional Brushing

```PY
import pandas as pd
import json

import streamlit as st
from streamlit_echarts import Map, st_echarts, st_pyecharts

from pyecharts import options as opts
from pyecharts.charts import Bar, Grid

df = pd.read_csv('lod14.csv', sep = ',')

# map - top
grp_Country = df.groupby(['Country']).sum()[['Sales']].reset_index()
maxmin = grp_Country['Sales'].sort_values(ascending = False).tolist()

map_data = grp_Country.copy()
map_data.columns = ['name','value']
map_data = map_data.to_json(orient = 'records')
map_data = json.loads(map_data)

with open("world.json", "r") as f:
    map = Map(
        "world",
        json.loads(f.read()),
    )

map_options = {
    "title": {
      "text": 'Proportional Brushing',
      "subtext": 'How much does each country contribute to total sales by product?',
    },
    "tooltip": {
      "trigger": 'item',
      "formatter": "Country: <b>{b}</b> </br>Sales: <b>${c}</b>"
    },
    "visualMap": {
      "show": False,
      "min": maxmin[-1],
      "max": maxmin[0],
      "inRange": {"color": ["#ba684e", "#fcb76b", "#aad2e8", "#5882aa"]},
    },
    "series": [
      {
        "name": '',
        "type": 'map',
        "roam": True,
        "map": 'world',
        "emphasis": {
          "label": {
            "show": True
          }
        },
        "data": map_data
      }
    ]
  }

events = {
    "click": "function(params) { console.log(params.name); return params.name }"
}

country = st_echarts(map_options, events = events, height = 400, map = map)

# bars - bottom
grp_Market_Country_SubCat = df.groupby(['Market','Country','Sub-Category']).sum()[['Sales']].reset_index()

grp_Market_SubCat = df.groupby(['Market','Sub-Category']).sum()[['Sales']]
grp_Market_SubCat.columns = ['TotalSales_SubCat']
grp_Market_SubCat = grp_Market_SubCat.reset_index()

mrg_ = pd.merge(grp_Market_Country_SubCat, grp_Market_SubCat, on = ['Market','Sub-Category'], how = 'left')
mrg_['Pct'] = mrg_['Sales']/mrg_['TotalSales_SubCat']*100

Country = grp_Country['Country'].tolist()

if country in Country:
    idf = mrg_[mrg_['Country'] == country].sort_values(by = 'TotalSales_SubCat')
    market = idf['Market'].tolist()[0]
    sub_cat = idf['Sub-Category'].tolist()
    sales = idf['Sales'].tolist()
    totalsales = idf['TotalSales_SubCat'].tolist()
    pct = idf['Pct'].round(2).tolist()
else:
    country = 'All'
    market = 'All'
    idf = grp_Market_SubCat.sort_values(by = 'TotalSales_SubCat')
    sub_cat = idf['Sub-Category'].tolist()
    totalsales = idf['TotalSales_SubCat'].tolist()
    sales = totalsales
    pct = []

title_b = "Sales by Product Sub-Category in " + country + " Total Sales in " + market

b1 = (Bar(init_opts = opts.InitOpts(width = "450px", height = "450px"))
        .add_xaxis(sub_cat)
        .add_yaxis('', 
                   totalsales,
                   itemstyle_opts = opts.ItemStyleOpts(color = "#9fe5ef"),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "The total sales for <b>{b}</b> in <b>" + market + "</b> is <b>${c}</b>.")
                   )
        .add_yaxis('', 
                   sales,
                   gap = "-100%",
                   itemstyle_opts = opts.ItemStyleOpts(color = "#39b0c2"),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>" + country + "</b> contributed <b>${c}</b> to the total <b>{b}</b> sales in <b>" + market + "</b>.")
                   )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            title_opts = opts.TitleOpts(
                title = title_b,
                pos_left = 'center'),
            xaxis_opts = opts.AxisOpts(
                name = "Sales",
                name_location = 'center',
                name_gap = 30,
                min_interval = 200000,
                axislabel_opts = opts.LabelOpts(formatter = "${value}")),
            yaxis_opts = opts.AxisOpts(axistick_opts = opts.AxisTickOpts(is_show = False))
          )
        #.render_notebook()
    )

b2 = (Bar(init_opts = opts.InitOpts(width = "450px", height = "450px"))
        .add_xaxis(sub_cat)
        .add_yaxis('', 
                   pct,
                   itemstyle_opts = opts.ItemStyleOpts(color = "#dcddde" ),
                   tooltip_opts = opts.TooltipOpts(
                        trigger = "item", 
                        formatter = "<b>" + country + "</b> generates <b>{c}%</b> of total <b>{b}</b> sales in <b>" + market + "</b>.") 
                   )
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(
                    is_show = True,
                    color = '#000',
                    position = "right",
                    formatter = "{c} %"
                    ))
        .set_global_opts(
            xaxis_opts = opts.AxisOpts(
                name = "Percent of Total",
                name_location = 'center',
                name_gap = 30,
                axislabel_opts = opts.LabelOpts(formatter = "{value}%")),
            yaxis_opts = opts.AxisOpts(is_show = False),
            )
        #.render_notebook()
    )

grid = (
    Grid()
    .add(
        b1, 
        grid_opts = opts.GridOpts(pos_top = "10%", pos_right = "53%"), 
        )
    .add(
        b2, 
        grid_opts = opts.GridOpts(pos_top = "10%", pos_left = "53%"), 
        )
    # .render_notebook()
)

if country in Country:
    st_pyecharts(grid)
else:
    st_pyecharts(b1)
```

# Result

![PY14](https://user-images.githubusercontent.com/79496040/193129664-c5ff08af-147d-4d78-b89a-a45542bb3130.gif)
