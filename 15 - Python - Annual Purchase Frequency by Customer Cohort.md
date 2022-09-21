# Annual Purchase Frequency by Customer Cohort

```PY
import pandas as pd
import numpy as np

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB

import panel as pn
pn.extension('echarts')

from pyecharts import options as opts
from pyecharts.charts import Line
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod15.csv', sep = '\t')
df['OrderDate'] = pd.to_datetime(df['OrderDate'], format = "%Y-%m-%d")
df['OrderYear'] = df['OrderDate'].dt.year

# Annual Purchase Frequency
grp_CustOrderYear = df.groupby(['CustomerID','OrderYear']).nunique()[['OrderID']]
grp_CustOrderYear.columns = ['Freq']
grp_CustOrderYear = grp_CustOrderYear.reset_index()

# Customer Cohort
grp_Cust = df.groupby(['CustomerID']).min()[['OrderDate']]
grp_Cust['CohortYear'] = grp_Cust['OrderDate'].dt.year
grp_Cust = grp_Cust[['CohortYear']].reset_index()

grp_Cohort = grp_Cust.groupby(['CohortYear']).count()[['CustomerID']]
grp_Cohort.columns = ['CohortCust']
grp_Cohort = grp_Cohort.reset_index()

mrg_Cohort = pd.merge(grp_CustOrderYear, grp_Cust, on = ['CustomerID'], how = 'left')
grp_OrderYearCohortYearFreq = mrg_Cohort.groupby(['OrderYear','CohortYear','Freq']).nunique()
grp_OrderYearCohortYearFreq.columns = ['NumCust']
grp_OrderYearCohortYearFreq['CumNumCust'] = grp_OrderYearCohortYearFreq.iloc[::-1].groupby(level = [0, 1]).cumsum()
grp_OrderYearCohortYearFreq = grp_OrderYearCohortYearFreq.reset_index()

mrg_CohortCust = pd.merge(grp_OrderYearCohortYearFreq, grp_Cohort, on = ['CohortYear'], how = 'left')
mrg_CohortCust['Pct'] = (mrg_CohortCust['CumNumCust']/mrg_CohortCust['CohortCust']).mul(100).round(0)

result = mrg_CohortCust[['OrderYear','CohortYear','Freq','Pct']]

orderyear = result.OrderYear.unique().tolist()

Sel_Year = pn.widgets.Select(name = 'Order Year', options = orderyear)

cohortyear = result['CohortYear'].unique().tolist()

freq = [filtered[filtered['CohortYear'] == i].Freq.tolist() for i in cohortyear]
pct = [filtered[filtered['CohortYear'] == i].Pct.tolist() for i in cohortyear]

min = cohortyear[0]
strCohort = [str(cohortyear[i-min]) if freq[i-min] else "" for i in cohortyear]

js_stats = """function (params) {
        console.log(params);
        return 'In <b>""" + str(Sel_Year.value) + """</b>, <b>' + params.value[1] + '%</b> customer from the <b>' + params.seriesName + '</b> cohort purchased at least <b>' + params.value[0] + '</b> times.';
    }"""

# Line Chart
L = (
    Line()
    .add_xaxis(freq[0])
    .add_yaxis(strCohort[0], 
               pct[0], 
               label_opts = opts.LabelOpts(is_show = False))
    .add_yaxis(strCohort[1], 
               pct[1], 
               label_opts = opts.LabelOpts(is_show = False))
    .add_yaxis(strCohort[2], 
               pct[2], 
               label_opts = opts.LabelOpts(is_show = False))
    .add_yaxis(strCohort[3], 
               pct[3], 
               label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        xaxis_opts = opts.AxisOpts(
            type_ = 'value',
            interval = 1,
            axistick_opts = opts.AxisTickOpts(length = 1)),
        yaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "{value} %")),
        title_opts = opts.TitleOpts(
            title = "Annual Purchase Frequency by Customer Cohort",
            subtitle = "What percent of customers from each cohort (year of acquisition) purchased at least 1, 2, 3, N times in 2013?"),
        tooltip_opts = opts.TooltipOpts(
                trigger = "item",
                formatter = JsCode(js_stats)
                ),
        legend_opts = opts.LegendOpts(pos_bottom = "5%")
            )
    .render_notebook()
)

L

@pn.depends(Sel_Year.param.value)
def plot(Sel_Year):
    filtered = result[result['OrderYear'] == Sel_Year]
    cohortyear = result['CohortYear'].unique().tolist()

    freq = [filtered[filtered['CohortYear'] == i].Freq.tolist() for i in cohortyear]
    pct = [filtered[filtered['CohortYear'] == i].Pct.tolist() for i in cohortyear]

    min = cohortyear[0]
    strCohort = [str(cohortyear[i-min]) if freq[i-min] else "" for i in cohortyear]
        
    # Line Chart
    L = (
        Line()
        .add_xaxis(freq[0])
        .add_yaxis(strCohort[0], 
                   pct[0], 
                   label_opts = opts.LabelOpts(is_show = False))
        .add_yaxis(strCohort[1], 
                   pct[1], 
                   label_opts = opts.LabelOpts(is_show = False))
        .add_yaxis(strCohort[2], 
                   pct[2], 
                   label_opts = opts.LabelOpts(is_show = False))
        .add_yaxis(strCohort[3], 
                   pct[3], 
                   label_opts = opts.LabelOpts(is_show = False))
        .set_global_opts(
            xaxis_opts = opts.AxisOpts(
                type_ = 'value',
                interval = 1,
                axistick_opts = opts.AxisTickOpts(length = 1)),
            yaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "{value} %")),
            title_opts = opts.TitleOpts(
                title = "Annual Purchase Frequency by Customer Cohort",
                subtitle = "What percent of customers from each cohort (year of acquisition) purchased at least 1, 2, 3, N times in 2013?"),
            tooltip_opts = opts.TooltipOpts(
                    trigger = "item",
                    ),
            legend_opts = opts.LegendOpts(pos_bottom = "5%")
                )
        #.render_notebook()
        )
    
    return pn.pane.ECharts(L, width = 900, height = 500)

pn.Column(Sel_Year, plot).servable()  
```

# Result
