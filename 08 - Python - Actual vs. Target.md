# Actual vs. Target

```PY
import pandas as pd

import streamlit as st
from streamlit_echarts import st_echarts

df = pd.read_csv('lod08.csv', sep = '\t')

# hbar - left
grp_Prod_State = df.groupby(['State','Product']).sum()[['Profit','TargetProfit']]
grp_Prod_State['Diff'] = grp_Prod_State['Profit'] - grp_Prod_State['TargetProfit']
grp_Prod_State['Num_Prod_Profit_GT_Target'] = grp_Prod_State['Diff'].apply(lambda x : 1 if x > 0 else 0)
grp_Prod_State = grp_Prod_State.reset_index()

# hbar - right
grp_State = grp_Prod_State.groupby(['State']).agg({'Diff':'sum','Num_Prod_Profit_GT_Target':'sum','Product':'count'})
grp_State['Pct_Prod_Profit_GT_Target'] = (grp_State['Num_Prod_Profit_GT_Target']/grp_State['Product']).mul(100).round(1)
grp_State = grp_State.sort_values(by = 'Diff', ascending = True)[['Diff','Pct_Prod_Profit_GT_Target']]

State = grp_State.index.tolist()
Diff = grp_State.Diff.tolist()
Pct = grp_State.Pct_Prod_Profit_GT_Target.tolist()

# hbar - top
hbar = {
  "title": {
    "text": 'Actual vs. Target',
    "subtext": 'What percentage of products are meeting their profit target in each state?',
  },
  "tooltip": {
    "trigger": 'axis',
    "axisPointer": {"type": 'shadow'},
    },
  "grid": [{"right": '55%'},
           {"left": '55%'}],
  "xAxis": [
    {
      "name": 'Difference Between Profit and Target',
      "axisLabel": {"formatter": '$ {value}'},
      "nameLocation": 'center',
      "nameGap" : 40, 
      "type": 'value',
      "gridIndex": 0,
      "position": 'bottom',
      "splitLine": {"show": False}
    },
    {
      "name": '% of Product Above Target',
      "axisLabel": {"formatter": '{value} %'},
      "min":0,
      "max":100,
      "nameLocation": 'center',
      "nameGap" : 40, 
      "type": 'value',
      "gridIndex": 1,
      "position": 'bottom',
      "splitLine": {"show": False}
    }],
  "yAxis": [
    {
    "type": 'category',
    "gridIndex": 0,
    "axisLine": { "show": False },
    # "axisLabel": { "show": False },
    "axisTick": { "show": False },
    "splitLine": { "show": False },
    "data": State
    },
    {
    "type": 'category',
    "gridIndex": 1,
    "axisLine": { "show": False },
    "axisLabel": { "show": False },
    "axisTick": { "show": False },
    "splitLine": { "show": False },
    "data": State
    }],
  "visualMap": 
   {
    "min": -900,
    "max": 1200,
    "show": False,
    "dimension": 0,
    "inRange": {"color": ['#e16a31', '#fb9a57', '#afd3de','#8ecbe4', '#4e6789']}
    },
  "series": [
    {
      "type": 'bar',
      "stack": 'leftbar',
      "data": Diff,
      "xAxisIndex": 0,
      "yAxisIndex": 0 
    },
    {
      "type": 'bar',
      "stack": 'rightbar',
      "data": Pct,
      "xAxisIndex": 1,
      "yAxisIndex": 1
    }
  ]
}
events = {
    "click": "function(params) { console.log(params.name); return params.name }"
}
state = st_echarts(hbar, events = events, height = 450)

# bar - bottom
if state in State:
    idf_grp_Prod = df[df['State'] == state].groupby(['Product']).sum()[['Profit','TargetProfit']]
else:
    state = 'All'
    idf_grp_Prod = df.groupby(['Product']).sum()[['Profit','TargetProfit']]

idf_grp_Prod = idf_grp_Prod.sort_values(by = 'Profit', ascending = False)
idf_grp_Prod['Color'] = idf_grp_Prod.apply(lambda x: 1 if x['Profit'] > x['TargetProfit'] else -1, axis = 1)

ds = [list(z) for z in zip(idf_grp_Prod.index, idf_grp_Prod['Color'], idf_grp_Prod['Profit'], idf_grp_Prod['TargetProfit'])]
ds.insert(0, ['Prod', 'Color', 'Profit', 'Target'])

title_b = 'Profit vs Target Profit by Product in ' + state

bar_b = {
  "dataset": {
    "source": ds
  },
  "title": {
    "text": title_b,
    "left": 'center',
  },
  "tooltip": {
    "trigger": 'axis',
    "axisPointer": {"type": 'cross'}
  },
  "xAxis": [
    {
      "type": 'category',
      "axisPointer": {"type": 'shadow'}
    }
  ],
  "yAxis": [
    {
      "type": 'value',
      "name": 'Profit',
      "splitLine": {"show": False}
    },
  ],
  "visualMap": {
      "min": -1,
      "max": 1,
      "show": False,
      "dimension": 1,
      "inRange": {"color": ['#ff8e49', '#51a0d4']}
      },
  "series": [
    {
      "name": 'Profit',
      "type": 'bar',
      "encode": {"x": 'Prod', "y": 'Profit'}
    },
    {
      "name": 'Target',
      "type": 'line',
      "encode": {"x": 'Prod', "y": 'Target'},
      "symbol": 'circle',
      "symbolSize": 10,
      "lineStyle": {"width": 0}
    }
  ]
}

st_echarts(bar_b, height = 250)
```

# Result

![PY08](https://user-images.githubusercontent.com/79496040/192872558-50abf866-e5fe-4ff4-a08b-4eef242085fd.gif)

### Comments

To-Do: Customize tooltips; 
