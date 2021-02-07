# matplotlib

```py
# matplotlib日本語化
!pip install japanize-matplotlib
import japanize_matplotlib

# sns.set()でdefaultが変わってしまうよう
import matplotlib.pyplot as plt
import seaborn as sns
import japanize_matplotlib
plt.style.use('default')
# plt.style.use('fivethirtyeight')
sns.set(font="IPAexGothic")
# sns.set()の内部でset_style(style, rc={"font.family": font}) というコードが実行されており、ここでseabornデフォルトのsans-serifが設定されてしまっているようです。[https://github.com/uehara1414/japanize-matplotlib/issues/1]
```

シンプル
```py
import matplotlib.pyplot as plt
fig, ax = plt.subplots(figsize=(12, 6))

# DataFrame
target_site_id_list = ['69452', '69453']
df = df_itr.query('SITE_ID == @target_site_id_list') \
    .pivot_table(index=['PERIOD'],
              columns=['SITE_NAME'],            # pivot後の列
              values=['EXISTING_PROFILES', 'NEWLY_TAGGED_PROFILES'], # pivot後の行
              aggfunc=sum)
df.plot(ax=ax)
ax.set(xlabel='PERIOD', ylabel='count num')
ax.grid(which='both', axis='both')
```

必要なら追加していく
```py
import matplotlib as mpl
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
fig, ax = plt.subplots(figsize=(12, 6))
ax.plot(df)
ax.set(xlabel='PERIOD', ylabel='count num',
    #    xlim=xlim, ylim=ylim,
      title='About as simple as it gets, folks')
# ax.set_yscale('log', basey=10) # 対数軸
ax.set_xticklabels(ax.get_xticklabels(), rotation=0, ha='center')
# ax.xaxis.set_major_locator(mpl.ticker.AutoLocator())
# ax.yaxis.set_major_locator(mpl.ticker.AutoLocator())
ax.xaxis.set_major_locator(mpl.ticker.MultipleLocator(1)) # 目盛り間隔
ax.yaxis.set_major_formatter(mpl.ticker.ScalarFormatter(1))
# ax.xaxis.set_major_formatter(mpl.ticker.PercentFormatter(1, 0, 'pct')) # %表示
# ax.yaxis.set_major_formatter(mpl.ticker.StrMethodFormatter('{x:+,.0f}')) # str.format()
ax.xaxis.set_major_formatter(mdates.DateFormatter("%m\n%d")) # 日付の場合のフォーマット
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda x, loc: "{:,}".format(int(x)))) # 三桁カンマの設定
# ax.xaxis.set_major_formatter(mpl.ticker.NullFormatter()) # 目盛ラベルなし
ax.legend(bbox_to_anchor=(1.05, 1), loc='upper left', borderaxespad=0, fontsize=18)
ax.grid(which='major', axis='both') # which='both'


plt.style.use('ggplot')  # いい感じのスタイルシート
plt.style.use('default')
plt.style.use('bmh')
plt.style.use('dark_background')
plt.style.use('fivethirtyeight')
plt.style.use('ggplot')
plt.style.use('grayscale')
plt.style.use('seaborn-bright')
plt.style.use('seaborn-colorblind')
plt.style.use('seaborn-dark')
plt.style.use('seaborn-dark-palette')
plt.style.use('seaborn-darkgrid')
plt.style.use('seaborn-deep')
plt.style.use('seaborn-muted')
plt.style.use('seaborn-pastel')
plt.style.use('seaborn-ticks')
plt.style.use('seaborn-white')
plt.style.use('seaborn-whitegrid')
```
https://qiita.com/eriksoon/items/b93030ba4dc686ecfbba#%E7%B5%84%E3%81%BF%E5%90%88%E3%82%8F%E3%81%9B%E3%81%A6%E4%BD%BF%E3%81%86%E3%81%93%E3%81%A8%E3%82%82
https://qiita.com/eriksoon/items/b93030ba4dc686ecfbba#%E7%B5%84%E3%81%BF%E5%90%88%E3%82%8F%E3%81%9B%E3%81%A6%E4%BD%BF%E3%81%86%E3%81%93%E3%81%A8%E3%82%82




```py
# MatplotlibのAxesSubplotオブジェクトとして扱う
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
df.plot(ax=ax)
```

## ベン図
```py

import matplotlib.pyplot as plt
from matplotlib_venn import venn2

def plot_venn_dataframes(df1:pd.DataFrame, df2:pd.DataFrame, columns:list, set_labels:tuple):
    """
    df1, df2 のベン図をplot
    - columns: df1, df2共通 columnを指定
    - set_labels: df1とdf2の説明
    """
    ncols = 6
    nrows = (len(columns) // ncols)
    fig, axes = plt.subplots(figsize=(3 * ncols, 3 * nrows), ncols=ncols, nrows=nrows)
    axes = np.ravel(axes)
    for c, ax in zip(columns, axes):
        venn2(subsets=(set(df1[ｃ]), set(df2[ｃ])), set_labels=set_labels, ax=ax)
        ax.set_title(c)
    fig.tight_layout()

plot_venn_dataframes(
    dataset.meta.query('date < "2020-08-01"'),
    dataset.meta.query('date >= "2020-08-01"'),
    list(set(dataset.meta.columns) & set(dataset.meta.columns)),
    set_labels=('Train', 'Test')
    )
```
