# plotly

# plotly.express
## ペアプロット
```py
import plotly.express as px
# scatter_matrix の color はプロットの色分け（dfのカラム名）
fig = px.scatter_matrix(df, color='PF')
fig.update_layout(width=1200, height=1200)
fig.show()
```

## 散布図＆ボックス図
```py
# trendline は最小2乗回帰（OLS：Ordinary Least Squares regression）
fig = px.scatter(df, x='fraud rate', y='saved click',
                 marginal_y="box", marginal_x="box",
                 size='saved click', size_max=50,
                 color='PF', trendline="ols")
fig.show()
```

## 散布図＆ボックス図(3D)
```py
# size: プロットの大きさを表すカラム
# size_max: sizeの制限
fig = px.scatter_3d(df, x='fraud rate', y='saved click', z='total',
                    color='業種', size='saved click', size_max=50)
fig.show()
```


## ライン
```py
import plotly.express as px
import plotly

layout = plotly.graph_objs.Layout(
    width=1200, height=1200,
    )

fig = px.line(df2, x='Date', y="入出金", color="入払区分",
              facet_col="Description",
            #   facet_row="Description",
              facet_col_wrap=2)

# データ点にマーク
fig.update_traces(mode='lines+markers')
fig.update_yaxes(tickformat="#,##0")

fig.update_layout(layout)
fig.show()
```

## 積み上げ棒グラフ
```py
import plotly.express as px
import plotly

layout = plotly.graph_objs.Layout(
    width=1200, height=600,
    )

fig = px.bar(df2, x='Date', y="入出金", color="Description")

fig.update_yaxes(tickformat="#,##0")
fig.update_xaxes(tickformat="%_y/%-m")

fig.update_layout(layout)
fig.show()
```

# HTML保存
```py
# htmlへ書き出し
fig.write_html("サンプルデータ_全量_業種別カラー.html")
```

可視化のやりかたまとめてきなのがわかりやすい↓
[A Simple Tutorial To Data Visualization | Kaggle](https://www.kaggle.com/vanshjatana/a-simple-tutorial-to-data-visualization#Rel-plot)

公式の機能別のサイト

https://plotly.com/python/reference/



# plotly.graph_objs.Layout
```py
import plotly.express as px
import plotly
layout = plotly.graph_objs.Layout(xaxis_type='date',
    title_text='',
    xaxis_showgrid=False, # grid非表示
    yaxis_showgrid=False, # grid非表示
    width = 1400, height = 450, # グラフエリアサイズ
    # autosize = True,  # HTMLで表示したときページに合わせてリサイズするかどうか
    # showlegend = False, # 凡例を表示するかどうか
    legend = dict(
                  font = dict(size=12, color='black'),  # 凡例のフォント設定
                  x = 1.02, xanchor = 'left',  # 凡例の表示場所の設定
                  y = 1, yanchor = 'auto',
                  bordercolor = '#444',
                  borderwidth = 0,  # 凡例を囲む枠線の設定
                  ),
    xaxis = dict(
                # title=dict(text='date', font=dict(size=10, color='black')),
                type='date',        # 'linear'、'log'、'date'などに設定可能
                tick0='2020-02-01', # 開始日設定：tick0
                autorange=True, # 軸の向きを反転させたい autorange='reversed'
                dtick=86400000.0*2,  # 表示間隔(tick: 複数日ごとのラベルは、n * 86400000.0 で表示。dtick = 'D')
            #    tickmode = 'auto',  # 目盛りの刻み方(目盛り関連の設定項目は他にもいくつかあります)
                tickformat="%_m/%-d",  # tickformat="%Y-%m-%d" : 2018-05-01 , tickformat="%_m/%-d" : 5/1 https://github.com/d3/d3-time-format/blob/master/README.md
                tickangle=45,
                tickfont=dict(family='Rockwell', color='black', size=14),
            #    range = [0, 1],
            #    scaleanchor = 'x1', scaleratio = 1,  # 他の軸とのスケールを固定したいときに使う
                ),
    yaxis = dict(
                tickformat="#,##0", # 3桁カンマ区切り , 指数表記 : e https://github.com/d3/d3-format/blob/master/README.md#locale_format
                nticks=50,
                tick0=0,
                rangemode='tozero',
                #  ticks="outside", #
                tickwidth=2,
                tickcolor='black',
                #  ticklen=10,
                #  showticklabels=False # tick非表示
                ),
)

```

シンプルバージョン（ただの散布図など）
```py
import plotly
layout = plotly.graph_objs.Layout(
    width=400, height=400,
    xaxis=dict(range=[0, 1], scaleanchor='y', scaleratio=1),
    yaxis=dict(range=[0, 1]),
    #   legend=dict(x=1.02, xanchor='left', y=1, yanchor='auto')
    showlegend=False
    )

# 線グラフの縦軸を指数表記から数字へ
layout = plotly.graph_objs.Layout(
    width=1200, height=600,
    yaxis = dict(tickformat="#,##0")
    )
```


```py
fig = px.line(df, x="PERIOD", y="PROFILES_NUM_SUM",
    color='CAT_SITE_PROF', # multiple linesを色指定
    #  facet_col="New_PK" # 種類別に別subplotとして出力
    )
fig.update_layout(layout)
fig.show()

```

縦線を入れたい

https://plotly.com/python/shapes/


```py
fig.add_shape(dict(type="line",
    x0=0, y0=0,
    x1=1, y1=1,
    line=dict(color="RoyalBlue", width=1)))

```


# pandasのバックエンドをplotlyに

plotly 4.8.0以降、pandas1.0以降
!pip install -U plotly

```py
import plotly.express as px
import plotly
pd.options.plotting.backend = "plotly"
# pd.options.plotting.backend = 'matplotlib' # デフォルト

layout = plotly.graph_objs.Layout(
    width=500, height=300,
    )

# 複数ラインも簡単にに引ける
fig = data.plot(x='week_n', y=['total_click_norm', 'fraud_rate_norm'])

# データ点にマーク
fig.update_traces(mode='lines+markers')
fig.update_yaxes(tickformat="#,##0")

# fig.update_layout(layout)
fig.show()
```
