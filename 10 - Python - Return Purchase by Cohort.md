# Return Purchase by Cohort

```PY
import pandas as pd
import numpy as np
from datetime import date

from pyecharts import options as opts
from pyecharts.charts import HeatMap
from pyecharts.commons.utils import JsCode

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_NOTEBOOK

df = pd.read_csv('lod10.csv', sep = ',')

df['OrderDate'] = pd.to_datetime(df['OrderDate'], format = "%Y-%m-%d")

grp_CustID_1stPurchase = df.groupby(['CustomerID']).min()[['OrderDate']].reset_index()
grp_CustID_1stPurchase.columns = ['CustomerID','1stPurchase']

grp_CustID_2ndPurchase = df.drop_duplicates().groupby(['CustomerID'])['OrderDate'].nsmallest(2).reset_index()
grp_CustID_2ndPurchase = grp_CustID_2ndPurchase[['CustomerID','OrderDate']]
grp_CustID_2ndPurchase.columns = ['CustomerID','2ndPurchase']

combined = pd.merge(grp_CustID_1stPurchase, grp_CustID_2ndPurchase, how = "left", on = ['CustomerID'])

combined['Seq'] = combined.groupby(['CustomerID']).cumcount() + 1
combined = combined.reset_index()

temp = combined.groupby(['CustomerID']).count()[['Seq']]
temp.columns = ['Cnt']
temp = temp.reset_index()

combined = pd.merge(combined, temp, how = 'left', on = ['CustomerID'])

combined = combined[(combined['Seq'] == 2) | (combined['Cnt'] == 1)]

combined['2ndPurchase'] = combined.apply(lambda x : date(2000,1,1) if x['2ndPurchase'] == x['1stPurchase'] else x['2ndPurchase'], axis = 1)

combined['1stPurchase'] = pd.to_datetime(combined['1stPurchase'], format = "%Y-%m-d")
combined['2ndPurchase'] = pd.to_datetime(combined['2ndPurchase'], format = "%Y-%m-d")

combined['YQ'] = combined['1stPurchase'].dt.year.astype(str) + '-Q' + combined['1stPurchase'].dt.quarter.astype(str)
combined['Diff'] = (combined['2ndPurchase'].dt.year - combined['1stPurchase'].dt.year) * 4 + (combined['2ndPurchase'].dt.quarter - combined['1stPurchase'].dt.quarter)

combined['Diff'] = combined['Diff'].apply(lambda x : "Lapsed" if x < 0 else x)

# Before category on Return Freq
B_Cat = combined.groupby(['YQ','Diff']).nunique()[['CustomerID']].reset_index()
B_Cat.columns = ['YQ','Diff','DistCnt']
B_Cat.info()

# After category on Return Freq
num = combined['Diff'].nunique()
sorted_diff = ["Lapsed" if i == 0 else i - 1 for i in range(num + 1)]

combined['Diff'] = pd.Categorical(combined['Diff'], categories = sorted_diff)

A_Cat = combined.groupby(['YQ','Diff']).nunique()[['CustomerID']].reset_index()
A_Cat.columns = ['YQ','Diff','DistCnt']
A_Cat.info()

Return_Freq = pd.merge(A_Cat, B_Cat, on = ['YQ', 'Diff', 'DistCnt'], how = 'inner')

value = [ list(z) for z in zip(Return_Freq.Diff, Return_Freq.YQ, Return_Freq.DistCnt)]

min, max = Return_Freq.DistCnt.min(), Return_Freq.DistCnt.max()
min, max

js_formatter = """function (x) {
        console.log(x);
        return x.marker + 'Among customers who first made a purchase in  <b>' + x.value[0] + '</b>, <br> <b>' + x.value[2] + '</b> waited <b>' + x.value[1] + '</b> quarters until their second purchase. ';
    }"""

H = (
    HeatMap()
    .add_xaxis(Return_Freq.Diff)
    .add_yaxis(
        "",
        Return_Freq.YQ,
        value,
    )
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "Return Purchase by Cohort",
            subtitle = "After customers are acquired, how many quarters elapse before they make another purchase?"),
        xaxis_opts = opts.AxisOpts(
            type_ = "category",
            # position = "top",
            minor_tick_opts = None,
            splitarea_opts = opts.SplitAreaOpts(
                is_show=True, 
                areastyle_opts = opts.AreaStyleOpts(opacity = 1)
            ),
        ),
        yaxis_opts = opts.AxisOpts(
            type_ = "category",
            is_inverse = True,
            splitarea_opts = opts.SplitAreaOpts(
                is_show = True, 
                areastyle_opts = opts.AreaStyleOpts(opacity = 1)
            ),
        ),
        tooltip_opts = opts.TooltipOpts(
            trigger = "item", 
            formatter = JsCode(js_formatter)
            ),
        visualmap_opts = opts.VisualMapOpts(
            is_show = False,
            min_ = min, max_ = max, 
            range_color = ["#b8e2d7", "#77bec8", "#368eae", "#2c5985"]
        ),
    )
    .render_notebook()
)
H
```

# Result

![PY10](https://user-images.githubusercontent.com/79496040/191984210-32d328e9-db18-48c7-92ee-382703c7c1b5.gif)

