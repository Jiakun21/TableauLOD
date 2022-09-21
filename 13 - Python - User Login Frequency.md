# User Login Frequency

```PY
import pandas as pd
import numpy as np

from pyecharts.globals import CurrentConfig, NotebookType
CurrentConfig.NOTEBOOK_TYPE = NotebookType.JUPYTER_LAB

import panel as pn
pn.extension('echarts')

from pyecharts import options as opts
from pyecharts.charts import Bar
from pyecharts.commons.utils import JsCode

df = pd.read_csv('lod13.csv', sep = '\t')
df['LoginDate'] = pd.to_datetime(df['LoginDate'], format = "%Y-%m-%d")

grp_Market_UserID = df.groupby(['Market','UserID']).agg(First_Login = ('LoginDate','min'),
                                                        Last_Login = ('LoginDate','max'),
                                                        Total_Logins = ('LoginDate','nunique'))

grp_Market_UserID['Total_Months'] = (grp_Market_UserID['Last_Login'].dt.year-grp_Market_UserID['First_Login'].dt.year)*12+(grp_Market_UserID['Last_Login'].dt.month-grp_Market_UserID['First_Login'].dt.month)
grp_Market_UserID['Login_Freq'] = grp_Market_UserID['Total_Months']/grp_Market_UserID['Total_Logins']

grp_Market_UserID['Round_Login_Freq'] = grp_Market_UserID['Login_Freq'].apply(lambda x : int(x) + 1 if x - int(x) >= 0.5 else int(x))

grp_Market_UserID = grp_Market_UserID[['Login_Freq','Round_Login_Freq']]

grp_Market = grp_Market_UserID.reset_index().groupby(['Market']).agg(Total_Users = ('UserID','nunique'),
                                                                     Avg_Login_Freq = ('Login_Freq','mean'))

grp_Market_RoundLoginFreq = grp_Market_UserID.reset_index().groupby(['Market','Round_Login_Freq']).agg(Users = ('UserID','nunique'))

result = pd.merge(grp_Market_RoundLoginFreq.reset_index(), grp_Market.reset_index(), on = ['Market'], how = 'left')
result['Pct'] = (result['Users']/result['Total_Users']).mul(100).round(2)
result['Diff'] = result['Round_Login_Freq'] - result['Avg_Login_Freq']
result = result.drop(columns = ['Users','Total_Users'])

market = result.Market.unique().tolist()

Sel_Market = pn.widgets.Select(name = 'Market', options = market)

color_function = """
        function (params) {
            if (params.value > 0 ) {
                return '#5b7291';
            } else { 
                return '#e1713f';
            }
        }
        """

b = (Bar()
    .add_xaxis(filtered.Round_Login_Freq.tolist())
    .add_yaxis('', 
               filtered.Pct.tolist(), 
                )
    .set_series_opts(label_opts = opts.LabelOpts(is_show = False))
    .set_global_opts(
        title_opts = opts.TitleOpts(
            title = "User Login Frequency",
            subtitle = "What percent of users log in once every month, 2 months, N months?"))
    .render_notebook()
)

b

@pn.depends(Sel_Market.param.value)
def plot(Sel_Market):
    
    ifiltered = result[result['Market'] == Sel_Market]
    avg_login_freq = np.round(ifiltered['Avg_Login_Freq'].mean(),3)

    b = (Bar()
    .add_xaxis(ifiltered.Round_Login_Freq.tolist())
    .add_yaxis('', 
               ifiltered.Pct.tolist(),
               itemstyle_opts = opts.ItemStyleOpts(
                       color = '#5b7291')
                )
    .set_series_opts(
        label_opts = opts.LabelOpts(is_show = False),
        markline_opts = opts.MarkLineOpts(
            data = [opts.MarkLineItem(x = avg_login_freq)],
            linestyle_opts = opts.LineStyleOpts(color = "#080808", type_ = "dashed"))
        )
    .set_global_opts(
        yaxis_opts = opts.AxisOpts(axislabel_opts = opts.LabelOpts(formatter = "{value} %")),
        title_opts = opts.TitleOpts(
            title = "User Login Frequency",
            subtitle = "What percent of users log in once every month, 2 months, N months?"),
        tooltip_opts = opts.TooltipOpts(
                trigger = "item", 
                formatter = "<b>{c}%</b> of users log in once every <b>{b}</b> months. "),
            )
    #.render_notebook()
    )

    return pn.pane.ECharts(b, width = 900, height = 500)

pn.Column(Sel_Market, plot).servable()
```

# Result
