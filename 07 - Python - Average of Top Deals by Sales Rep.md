# Average of Top Deals by Sales Rep

```PY
import pandas as pd
import json

import streamlit as st
from streamlit_echarts import Map, st_echarts, st_pyecharts

from pyecharts import options as opts
from pyecharts.charts import Bar
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod07.csv', sep = ',')

MaxSales_Country_SalesRep = df.groupby(['Country','Sales Rep']).max()[['Sales']].reset_index()
Avg_Top_Deal = MaxSales_Country_SalesRep.groupby(['Country']).mean()[['Sales']].round(0).reset_index()
maxmin = Avg_Top_Deal['Sales'].sort_values(ascending = False).tolist()

map_data = Avg_Top_Deal.copy()
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
      "text": 'Average of Top Deals by Sales Rep',
      "subtext": 'What is the top deal closed by the sales rep, and the average of these deals by country?',
    },
    "tooltip": {
      "trigger": 'item',
      "formatter": "<b>{b}'s</b> average largest deal size is <b>${c}</b>."
    },
    "visualMap": {
      "left": 'right',
      "orient": 'horizontal',
      "min": maxmin[-1],
      "max": maxmin[0],
      "inRange": {"color": ["#ba684e", "#fcb76b", "#aad2e8", "#5882aa"]},
      "text": ['High', 'Low'],
      # "calculable": True
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

# bar - bottom
Country = Avg_Top_Deal['Country'].tolist()
## MaxSales per SalesRep
if country in Country:
    idf_grp_SalesRep = df[df['Country'] == country].groupby(['Sales Rep']).max()[['Sales']]
else:
    country = 'All'
    idf_grp_SalesRep = df.groupby(['Sales Rep']).max()[['Sales']]

idf_grp_SalesRep = idf_grp_SalesRep.reset_index().round().sort_values(by = 'Sales')

title_b = "Top Deal by Sales Rep in " + country + " Compared to National Average"
Avg_MaxSales = round(idf_grp_SalesRep['Sales'].mean())

salesrep = idf_grp_SalesRep['Sales Rep'].tolist()
sales = idf_grp_SalesRep['Sales'].tolist()

js_color = """function (params) {
        if (params.value > """ + str(Avg_MaxSales) + """) {
                return '#90cee3';
            } else {
                return '#ffbb78';
            }
    }"""

b = (Bar()
        .add_xaxis(salesrep)
        .add_yaxis('', 
                   sales,
                   itemstyle_opts = opts.ItemStyleOpts(
                       color = JsCode(js_color)
                   ),
                   markline_opts = opts.MarkLineOpts(
                       data = [opts.MarkLineItem(type_ = "average", )],
                       precision = 0,
                       is_silent = True,
                       label_opts = opts.LabelOpts(is_show = False),
                       linestyle_opts = opts.LineStyleOpts(width = 1, color = '#000000', type_ = 'dash', )
                    ),)
        .reversal_axis()
        .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            xaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "$ {value}")),
            yaxis_opts = opts.AxisOpts(axistick_opts = opts.AxisTickOpts(is_show = False)),
            title_opts = opts.TitleOpts(
                title = "Top Deal by Sales Rep in " + country + " Compared to National Average",
                pos_left = "center"),
            tooltip_opts = opts.TooltipOpts(
                trigger = "item", 
                formatter = "<b>{b}'s</b> largest deal size is <b>${c}</b></br> compared to the average of <b>$" + str(Avg_MaxSales) + "</b>.")
        )
    )

st_pyecharts(b)
```

# Result

![PY07](https://user-images.githubusercontent.com/79496040/193074028-2dfeacad-c4b4-4ab7-aca8-c5bcb3ea7db0.gif)

