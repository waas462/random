# pandas

# DataFrame作成&変換
## read csv
```py
# pandasでcsvやexcelを読み込む場合に邪魔なコンマを消す方法
df = read_csv(filename, thousands=',')
df = get_merged_csv(files, sep=',', encoding="SHIFT-JIS", thousands=',', skiprows=5)
```

# 時系列データの読み込み
```py
pd.read_csv('./train.csv', parse_dates=['date'], index_col='date')
```

## 配列から作成
    pd.DataFrame({
        'session_id': train_sessions
    })


**複数のCSVファイルを読み込**

    import glob
    import pandas as pd

    # ディレクトリ以下のファイルリスト
    data_dir = '/content/2nd_test/*'
    files = glob.glob(data_dir)

    def get_merged_csv(flist, **kwargs):
        return pd.concat([pd.read_csv(f, **kwargs) for f in flist], ignore_index=True)

    df_itr = get_merged_csv(files, sep='\t', skiprows=5)

**ファイル名を条件指定して複数ファイルの読み込み**

    import os
    import glob
    import pandas as pd

    def get_merged_csv(flist, **kwargs):
        return pd.concat([pd.read_csv(f, **kwargs) for f in flist], ignore_index=True)

    path = 'C:/Users/csvfiles'
    fmask = os.path.join(path, '*mask*.csv')

    df = get_merged_csv(glob.glob(fmask), index_col=None, usecols=['col1', 'col3'])

    print(df.head())

**特定列の読み込み**

    data = pd.read_csv('data.csv', usecols=['ID', 'Status'])

**読込でメモリに乗らないサイズの場合**

    # 前処理（これでサイズが減らないとchunkしても乗らない）
    def preprocess(x):
        # 不要な行, 列のフィルタなど、データサイズを削減する処理
        return x

    # 返り値は DataFrame ではなく TextFileReader インスタンス
    reader = pd.read_csv(fname, skiprows=[0, 1], chunksize=50)

    # 前処理して積み上げ
    df = pd.concat((preprocess(r) for r in reader), ignore_index=True)
## dict → df
    # 辞書からdfを作成（indexの数があっている必要がある）
    pd.DataFrame(dic, index=['i',])
## df → list
    df.columns.values
## df → dict
    dict(df_category.reset_index().values)


# 代入

[pandasで条件に応じて値を代入（where, mask） | note.nkmk.me](https://note.nkmk.me/python-pandas-where-mask/)

# 抽出
## 特定列をgroupbyしてみたい
    # groupbyで指定した列はindexで返される事を利用
    df2.groupby(['week_n', 'early_week', 'early_week_tmp', 'test']).count().index
## 特定の文字列を含むレコードを抽出
    df[df['colum name'].str.contains('find word')]
## query

※カラムがobject型やstr型の場合、値を""で括る必要あり

    df_itr.query('PERIOD >= "2020-01-01"')
    df_itr.query('CATEGORY_PATH2 == "3rd Party Data (3rd Party Data)"')
    df_itr.query('CATEGORY_PATH6.isnull()', engine='python')
    df_itr.query('CATEGORY_PATH5.str.contains("類推")==False', engine='python')
    # 変数を条件に使う
    ID = "67845"
    df.query('SITE_ID == @ID')
    df.query(f'site_id == {ID}')

    # where not like in
    not_like_description = ['abc']
    df2 = df2.query(f"Description != {not_like_description}")
## 複数キーワード抽出 isin
    df[df['state'].isin(['NY', 'TX'])]
## カラムのAND条件
    train_[(train_['installation_id'] == '0001e90f') & (train_['event_id'] == '5e812b27')]
## インデックスを条件に抽出
    # インデックスを条件に抽出
    df = df[df.index.get_level_values('year') >= 2018]
    df.iloc[df.index.get_loc('1009399'), 0:]
    df.query('index == "1009399"')
    # 別のDFのindex
    df_new.query(f'index == {list(sim_users.index)}')
    # ↑と同じだが速度はこちらがは早そう
    df_new.loc[sim_users.index, :]
## サンプリング
    df.sample(frac=0.04)
    df.sample(n=3, random_state=0)

    # グループを加味してサンプリング
    gdf = df.groupby('category')
    gdf.apply(lambda x: x.sample(n=2), random_state=42))

    # 元のグループの比率を維持してサンプリングしたい
    gdf.apply(lambda x: x.sample(n=round(len(x) * 0.5)))
## 重複行
    # 重複を判定する列を指定: 引数subset
    df.duplicated(subset='ユニークID')

    # 重複した行を抽出（keep='first' は重複した最初の行がFalse、lastは最後がFalse）
    df[df.duplicated(keep='first')].style

    # 重複削除後のDFを表示
    #  df[~df.duplicated()] と同じ
    df.drop_duplicates().style

    # 重複した行カウント
    df.duplicated().sum()


## ソート

sort_index() か、 sort_values()
# 絶対値でソート

    df.reindex(df.loc[:, i].abs().sort_values().index)

    pandas.Series.value_counts(normalize=False, 割合に変換
        sort=True,
        ascending=False, 結果をソートする
        bins=None, bin count
        dropna=True
    )

ソートしたい列のラベル（列名）複数列はリストで
byで複数列にした際は同じくリストで

    DataFrame.sort_values(
        by,
        axis=0,
        ascending=True,
        inplace=False,
        kind='quicksort',
        na_position='last')

axis=1で列をソートできる

    DataFrame.sort_index(
        axis=0,
        level=None,
        ascending=True,
        inplace=False,
        kind='quicksort',
        na_position='last',
        sort_remaining=True,
        by=None)

# ユニーク集計ソート

    pd.Series(df.iloc[:,1]).value_counts().sort_index()
# DataFrame操作
## 列追加

**列同士の文字列を結合**

    df['name_state'] = df['name'].str.cat(df['state'], sep='_')
    # dtypeは文字列である必要がある
    df2['yyyym'] = df2['year'].astype(str).str.cat(df2['month'].astype(str), sep='_')


    # 二つの文字列を結合
    df['early_week'] = df[['startday', 'week_n']].apply(lambda x: '{}-W{}'.format(x[0].year, x[1]), axis=1)



## ブール
    # series.str.containsで文字列検索を正規表現で
    df['店'].str.contains('昭和シェル.*|Ｄｒ．Ｄｒｉｖｅ.*') | df['店'].str.contains('西松屋.*')

    df['bool'] = False
    for word in list(replace_dict.keys()):
        df['bool'] = df['店'].str.contains(word) | df['bool']
## DF結合
    pd.merge(df_itr, df_rule_reduce, how='left', left_on='acount_id', right_on='acount')

sort=Falseにしないと、カラム順が昇順に並び替えられてしまう
※concatした時 dtypeが変わるケースがある（NaNになるときと思われる）[Python: pandas で DataFrame を連結したら dtype が int から float になって驚いた話 - CUBE SUGAR CONTAINER](https://blog.amedama.jp/entry/2018/05/12/175309)

    pd.concat([df_1, df_2], sort=False)

# 辞書をマージする (キー重複した場合は、Value をリスト化)

    def merge_dicts(dicts_list):
        d = {}
        for dict in dicts_list:
            for key in dict:
                try:
                    d[key].append(dict[key])
                except KeyError:
                    d[key] = [dict[key]]
        return d

    D = merge_dicts( [{'通勤手当': '30'},  {'通勤手当': '440'},  {'aa': '44'}])
    print(D)
## 分割

一つのカラムに複数情報（カンマ区切りなどで）が入っている場合に新たにカラムとして分割する

    sample_submission['id'].str.split('_', expand=True)
## 削除

subsetで指定した行に欠損値がある列を削除

    df.dropna(subset=[0, 4], axis=1)

行に1つでもNaNが含まれるとその行が削除

    df_sample.dropna(how='any', axis=0, inplace=True)


## 欠損値

pandasにおいては、None, np.nan, math.nanおよびpd.np.nanが欠損値NaNとして扱われる

nullの確認

    df['支払い金額'].isnull()

特定の行・列に欠損値がある列・行を削除

    df.dropna(subset=['age'])
    df.dropna(subset=['age', 'state'])
    # すべての値が欠損値である行・列を削除
    df.dropna(subset=['age', 'state'], how='all', axis=0)
    # 欠損値が一つでも含まれる行が削除
    df.dropna(subset=['age', 'state'], how='any', axis=0)
    # 欠損値ではない要素の数に応じて行・列を削除する
    df.dropna(thresh=3)

ゼロ埋め、アラインメント

    df_melt['user'] = df_melt['user'].map('{:04}'.format)

欠損値を置換（穴埋め）

    # 共通の値で一律に置換
    df.fillna(0)

    # 列ごとに異なる値で置換
    df.fillna({'name': 'XXX', 'age': 20, 'point': 0})

    # 列ごとに平均値、中央値、最頻値などで置換
    # numeric_only=Trueとすると対象が数値列に限定される。なお、その場合もbool型の列はTrue=1, False=0として処理対象となる
    # 対応する列の欠損値が平均値で置換される
    df.fillna(df.mean())

    # 中央値で置き換えたい場合はmedian()メソッドを使う。偶数個の場合は中央二つの値の平均値が中央値となる
    df.fillna(df.median())

    # ode()はpandas.DataFrameを返すのでiloc[0]で先頭行をpandas.Seriesとして取得する
    df.fillna(df.mode().iloc[0])

    # 前後の値で置換
    df.fillna(method='ffill') # 前の値
    df.fillna(method='bfill') # 後ろの値

    # 欠損値抽出
    df['point'].isnull()

    # 欠損値が一つでも含まれる行・列を抽出
    df2.isnull()

    # 行または列に一つでもTrueが含まれているとTrueを返す
    df2.isnull().any(axis=1)



## value置換

辞書を使った置換

    # dictをDFから生成
    item_name_dict = dict(df_category.reset_index().values)

    # mapで値を置換（keyと一致する要素がvalueに置き換えられる） ※ keyが存在しない場合、NaNになる
    result['item_name'] = result['item'].map(item_name_dict)

    # replaceで置換（keyと一致する要素がvalueに置き換えられる） ※ keyが存在しない場合は、元の値のまま
    result['item_name'] = result['item'].replace(item_name_dict)

    # dict正規表現
    replace_dict = {
        '西松屋.*' : '西松屋',
        'ホームズ.*|島忠.*' : 'ホームセンター',
        'ビバホーム.*' : 'ホームセンター',
        }
    df[['ご利用先など']].replace(replace_dict, regex=True).drop_duplicates().style


## column置換


    # カラムリネーム
    df_org.rename(columns={'A': 'a'}, index={'ONE': 'one'}, inplace=True)
    # ラムダ式や関数で一括処理
    df.rename(columns=str.lower, index=str.title)
    # 全カラムスペースを、_に置換
    df3 = df2.rename(columns=lambda s: s.replace(" ","_"))

列名にプレフィックス（接頭辞）、サフィックス（接尾辞）を追加

    df.add_prefix('X_')
    df.add_suffix('_X')

行名・列名をすべて新しい値にする

    df.index = [1, 2, 3]
    df.columns = ['a', 'b', 'c']


## pitvot & melt

pivot

    df.pivot_table(index=['industry'],  # pivot後の行
                  columns=['platform'], # pivot後の列
                  values='saved_click', # pivotの集計値
                  aggfunc='count')      # pivotの集計方法(aggfunc=sum, aggfunc='count')


    import numpy as np
    df_flights_pivot = pd.pivot_table(data=df,
                                      values=['NEWLY_TAGGED_PROFILES'],
                                      columns='PERIOD',
                                      index=['CATEGORY_PATH_0', 'CATEGORY_PATH_1'],
                                      margins=True, # 合計
                                      aggfunc=np.sum)

```

逆pivot(melt)
一つのカラムに複数情報が入っている場合、それを列に分解し、meltによって行として分解する（下では|で区切られたIDを例としている）
```

    # カラム名
    base_colmuns = df_rule.columns.values.tolist()
    siteid_columns = ['SITE_ID_'+ str(x) for x in df_rule['Site_ID'].str.split('|', expand=True).columns]

    # site idをカラムに分割後、元のDFへ結合
    df_rule = pd.concat([df_rule, df_rule['Site_ID'].str.split('|', expand=True)], axis=1)
    df_rule.columns = base_colmuns + siteid_columns

    # meltで１行ずつへ
    df_rule = pd.melt(df_rule, id_vars=base_colmuns,      # 軸にしたいカラム（そのまま残したいカラム）
                              value_vars=siteid_columns, # meltしたいカラム
                              var_name='item',          # meltしたカラム名が入ったカラム名
                              value_name='raiting'      # 値のカラム名
    )


## pivotしたものを元に戻す
https://stackoverflow.com/questions/51078388/how-to-melt-first-level-column-in-multiindex-with-pandas

    df=pd.DataFrame(np.zeros((3,6)))
    df.columns=pd.MultiIndex.from_arrays([['a','a','b','b','c','c'],[1,2,1,2,1,2]])
    df['a']=10
    df['b']=20
    df['c']=40

    print(df)

    print(df.unstack()
            .unstack(level=1)
            .reset_index(level=1, drop=True)
            .rename_axis('names')
            .reset_index())
## groupby

Python pandas データ選択処理をちょっと詳しく <中編> - StatsFragments
まとめたい

単純に全カラム

    df.sum()


    # keyで集約(行数はkey単位へ)
    df.groupby('sarary').sum()
    df.groupby('Year').apply(np.sum)
    # 行数は変わらず集計
    df.groupby('Year').transform(np.sum)
    # 行数を変えずに移動平均
    dt[["id", lag_col]].groupby("id")[lag_col].transform(lambda x : x.rolling(win).mean())

ユニークな値のサイズ

    df_itr.groupby('CATEGORY_ID').size()

グループ単位に処理する（下の例だとaccount_id毎にループ）

    for i, id in df.groupby('account_id', sort=False):
        print(id)

key単位の件数取得

    train.groupby(['event_id', 'event_code'])['event_id'].count()
    df_itr.groupby(['CATEGORY_ID', 'SITE_ID']).size()

複数カラムを一度に集計

    df_itr.groupby(['SITE_ID', 'SITE_NAME', 'PERIOD']) \
        .agg(category_count=('CATEGORY_ID', 'count'),    # category_count はカラム名
            exitst_sum=('EXISTING_PROFILES', 'sum'),
            newly_sum=('NEWLY_TAGGED_PROFILES', 'sum'))

統計量作成後、リネーム

    spectrum_agg = spectrum.groupby("spectrum_filename")["intensity"].agg(["max", "min", "mean", "std"])
    spectrum_agg.columns = ["intensity_" + c for c in spectrum_agg.columns]


# 集約関数作成して

    def peak_near_sum(x):
        i = np.argmax(x)
        z = x[i - 10:i + 10]
        return np.sum(z) / x[i]
    # aggに渡すとき、どの値を引数に渡すか指定する必要がある（下の"intensity"）
    spectrum.groupby('spectrum_filename')["intensity"].agg(peak_near_sum=peak_near_sum)

下のように書けば、別々の値に対して集約を一発で可能だし、分かりやすい
 https://qiita.com/mk_GF/items/9635b7fc84d38365470a

    spectrum.groupby('spectrum_filename').agg( \
      intensity_peak_near_sum=('intensity', peak_near_sum),
      intensity_max=('intensity', 'max'),
      intensity_min=('intensity', 'min'),
      intensity_mean=('intensity', 'mean'),
      intensity_std=('intensity', 'std')
      )


## 標準化
    # DataFrameの標準化（inverse使いたいのでsklearn）
    from sklearn.preprocessing import StandardScaler
    norm_target_col = ['total_click', 'fraud_rate', 'saved_click']
    sc = StandardScaler()
    sc.fit(df[norm_target_col])
    df_std = pd.DataFrame(sc.transform(df[norm_target_col]), columns=norm_target_col)
    df_std = pd.concat([df.drop(norm_target_col, axis=1), df_std], axis=1) # 元のカラム削除
    # sc.inverse_transform(result)


    # 標準化
    norm_target_col = ['total_click', 'fraud_rate', 'saved_click']

    for col in norm_target_col:
        data[col + '_norm'] = data[[col]].apply(lambda x : ((x - x.mean()) / x.std() ))

# データ型変換
## 型変換 astype
    df = df.astype({'a': float})
    df['staff'].astype(int)


## 日付

# 日付フォーマット変換

    df2['date'].dt.strftime('%Y-%m-%d')

    df['日付'] = pd.to_datetime(df['日付'])

日付リストの生成

    # 後ろへ30日ずつ5回分
    days1 = pd.date_range(end='2020-02-18', freq='30D', periods=5).to_list()
    # 前へ30日ずつ5回分
    days2 = pd.date_range(start='2020-02-18', freq='30D', periods=5).to_list()
    days = days1 + days2
    days.sort()



# 可視化
## 相関係数
    train.corr()['target'].sort_values()

`df.corr(df, method=)`
- 'pearson': ピアソンの積率相関係数（デフォルト）
- 'kendall': ケンドールの順位相関係数
- 'spearman': スピアマンの順位相関係数

## df.plot

複数の列を重ねてプロットしたい場合

    ax = df.plot(y='sepal_length')
    df.plot(y='sepal_width', ax=ax)

    # これだと一発
    df[['sepal_length', 'sepal_width']].plot()


## pandas-profiling
    # 最新バージョン
    !pip install pandas-profiling==2.8.0
    # matplotlib日本語化
    !pip install japanize-matplotlib

    import japanize_matplotlib
    import pandas as pd
    import pandas_profiling as pdp
    from IPython.display import HTML
    # 全サンプル使う必要はない
    profile = pdp.ProfileReport(df)
    profile.to_file("ProfileReport.html")
    HTML(filename='ProfileReport.html')
## DataFrameの表示
    pd.set_option('display.max_columns', 100)
    pd.set_option('display.max_rows', 100)

with句を使って一時的に表示範囲を広げたりするときに使う

    # 最大表示行数(defult:60)None全行表示
    with pd.option_context('display.max_rows', 100,
        'display.max_columns', 100, # 最大表示列数(defult:20)
        'display.width', 60, # 全体の最大表示幅(defult:80)この値を超えると改行される(ターミナルの場合)
        'max_colwidth', 80, # 列ごとの最大表示幅(defult:50)
        'display.colheader_justify', 'right', # 列名表示の右寄せ・左寄せ(defult:right)
        'display.precision', 6, # 小数点以下桁数(defult:6)元のデータの値は以降の桁の情報も保持している
        ):
        display(df)

dataframe へ符号に応じてデータフレーム列の値を緑または赤に色付けする関数を適用

    def color_negative_red(value):
        """
        Colors elements in a dateframe
        green if positive and red if
        negative. Does not color NaN
        values.
        """
        if value < 0:
            color = 'red'
        elif value > 0:
            color = 'green'
        else:
            color = 'black'
        return 'color: %s' % color
    df.style.applymap(color_negative_red, subset=['total_amt_usd_diff','total_amt_usd_pct_diff'])

データフレームに値の表示書式を適用する

    (df.style
    .applymap(color_negative_red, subset=['total_amt_usd_diff','total_amt_usd_pct_diff'])
    .format({'total_amt_usd_pct_diff': "{:.2%}"}))

データフレーム要素に任意のCSSを適用
[スタイルシートリファレンス（目的別）](http://www.htmq.com/style/)
シンプル表示

    # Set CSS properties for th elements in dataframe
    th_props = [
        ('font-size', '11px'),
        ('text-align', 'center'),
        ('font-weight', 'bold'),
        ('color', '#6d6d6d'),
        ('background-color', '#f7f7f9')
    ]
    # Set CSS properties for td elements in dataframe
    td_props = [
        ('font-size', '11px')
    ]
    # Set table styles
    styles = [
        dict(selector="th", props=th_props),
        dict(selector="td", props=td_props)
    ]
    df_pivot.style.set_table_styles(styles)


例：シンプル＋マウスポイント行色付け
# color (HTMLで使える色の文字列)
# https://colorate.azurewebsites.net/ja/ColorNames
# マウスポイントの行を色付け(th,tdに'background-color'を設定しているとダメ)

    def hover(hover_color="LightGray"):
        return dict(selector="tr:hover",
            props=[("background-color", "%s" % hover_color)])

    # CSS style
    styles = [
        hover(),
        dict(selector="th", # Table Header
            props=[('font-size', '11px'),
            ('text-align', 'center'),
            ('font-weight', 'bold'),
            ('border-bottom-color', 'GhostWhite'),
            ('border-bottom-style', 'ridge'), # ridge, solid
            ('color', '#6d6d6d'),
            # ('background-color', 'LightGray')
        ]),
        dict(selector="td", # Table Data
            props=[('font-size', '11px'),
            # ('background-color', 'GhostWhite'),
            ('border-collapse', 'collapse'), # separate, collapse
            ('border-bottom-color', 'GhostWhite'),
            ('border-bottom-style', 'ridge'),
            ('text-align', 'center'),
            ('white-space', 'nowrap')]), # セル内で折り返さない
            dict(selector="caption",
                props=[("caption-side", "bottom")])
    ]
    df_pivot.fillna("").style.set_table_styles(styles).set_caption("Hover to highlight.")


カスタム背景色のグラデーションとカスタムキャプションの適用

    # Set colormap equal to seaborns light green color palette
    cm = sns.light_palette("green", as_cmap=True)
    (df.style
    .background_gradient(cmap=cm, subset=['total_amt_usd_diff','total_amt_usd_pct_diff'])
    .highlight_max(subset=['total_amt_usd_diff','total_amt_usd_pct_diff'])
    .set_caption('This is a custom caption.')
    .format({'total_amt_usd_pct_diff': "{:.2%}"})
    .set_table_styles(styles))

# seabornのカラーマップを利用

    import seaborn as sns
    cm = sns.light_palette("green", as_cmap=True)
    df_pivot.fillna(0).style.background_gradient(cmap=cm)


## df.style
    # styleは上から順番につけられるっぽい（上書きされる）

    # 各セルの値に応じてカラーマップを適用
    # low, high: カラーマップの一部範囲の色のみ利用する場合
    # subset: 特定の列にのみ Styler を適用する場合
    df.style.background_gradient(cmap='winter', low=0., higt=100, subset=['values1'])


    # 列もしくは行の最大値（最小値）を指定して色分け
    df.style.highlight_max(axis=0, color='blue')
    df.style.highlight_min(axis=0, color='blue')


    # NaN を指定して色分け。
    df.style.highlight_null(null_color='red')


    # 各セルの値に応じて棒グラフのように背景色表示。
    Styler.bar()
    df.style.bar(subset=['A', 'B'], color='#d65f5f')


    # 各セルに対して CSS を返す関数を適用 (関数への入力はスカラー)
    Styler.applymap


    # 各列もしくは各行に対して CSS を返す関数を適用 (関数への入力は 各列/各行の値からなる Series )。
    Styler.apply


    # セルの値が 0 より小さい場合は 赤 / 太字で表示する場合は Styler.applymap。
    def highlight_negative(val):
        if val < 0:
            return 'color: {0}; font-weight: bold'.format('red')
        else:
            return 'color: {0}'.format('black')
    df.style.applymap(highlight_negative)


    # 各行について、values1 列の値が values2 列の値より大きいときに values1 列のみ 赤背景で表示したい。
    # 行ごとに関数を適用するため、axis=1 を指定する。
    # highlight_values1 関数が返す リストの各要素が、各列に適用される CSS に対応している。
    def highlight_values1(s):
        if s['values1'] > s['values2']:
            #
            return ['', 'background-color: red', '']
        else:
            # スタイル変更しない場合は空の文字列のリストを返す
            return [''] * len(s)
    df.style.apply(highlight_values1, axis=1)


    # フロートの精度を制御
    with pd.option_context('display.precision', 2):
        df.style.applymap(color_negative_red).apply(highlight_max))
        # フロートの精度を制御
        df.style.applymap(color_negative_red).apply(highlight_max).set_precision(2)
# ipython マジックコマンド

マジックコマンドの末尾に「？」を追加して実行すると、そのコマンドの「docstring」が表示され、末尾に「??」を追加すると、そのコマンドのソースコードが表示
%pwd?
%pwd??
Notebook上でOSのシェルのコマンドを実行する
!ls

ラインマジック

- %lsmagic：現在利用可能なマジックコマンドの一覧を表示してくれます。
- %magic：マジックコマンドの情報を表示してくれます。
- %time：これに続くコードの実行時間を測定し、結果を表示してくれます。
- %timeit：これに続くコードの実行時間を何度か測定し、その中で最速であった結果と平均を表示してくれます。
- %env：環境変数を取得したり、設定したりすることができます。
- %bash, %script：Shellコマンドを実行することができます。
- %who：現在宣言されている変数を表示してくれます。
- %whos：現在宣言されている変数とその型、内容を表示してくれます。
- %pwd：現在のディレクトリを表示します。
- %history：コードセルの実行履歴を一覧で表示してくれます。
- %ls：カレントディレクトリーのファイルを一覧で表示してくれます。
- %matplotlib inline：pyplotなどでグラフを描写すると結果が別ウインドーが開きそこに表示されますが、このマジックコマンドを使うとnotebook内にグラフが表示されるようになります。
- %matplotlib tk：別ウインドーにインタラクティブなグラフを表示してくれます。
- %matplotlib notebook：inlineを指定した場合と同様に、コードセルの下にグラフを表示してくれます。グラフはインタラクティブなグラフになります。
- %quickref：クイックレファレンス一覧を表示してくれます。

セルマジック
セルの先頭に記述する必要がある

- %%timeit：%timeitの機能をセル内のすべてのコードに適応します。
- %%html, %%HTML：htmlのコードの記述、実行を可能にします。
- %%!, %%sx, %%system：shellコマンドの実行を可能にします。
- %%js：Javascriptのコードの記述、実行を可能にします。
- %%ruby：rubyのコードの記述、実行を可能にします。
- %%perl：perlのコードの記述、実行を可能にします。
- %%python：pythonのコードの記述、実行を可能にしま





#




# その他
## DataFrame同士が同じである事
    # DFを比較できる条件（同じ形状、同じindex、同じcolumn）
    # DataFrame同士の要素一致確認（全ての要素が一致していれば片方の全要素数と同じになる）
    def dataframe_attr_match(df1, df2):
    print(f'DF 片方のsize:{df1.size}')
    print(f'DF 一致要素数:{(df1.sort_index(axis=1).sort_index(axis=0) == df2.sort_index(axis=1).sort_index(axis=0)).sum().sum()}')
    dataframe_attr_match(df_final, df_final2)


## 処理時間計測
    # 処理時間、メモリ、件数の比較
    import time
    for n in [1000, 10000, 100000]:
        _df = df.sample(n=n, random_state=0)
        t1 = time.time()
        df_final, df_final_sparse = get_pre_df(df.sample(n=n, random_state=0))
        mem = df_final.memory_usage(deep=True).sum() / 1024**2
        t2 = time.time()
    print(f'{n}\t{mem}\t{t2-t1}')


## **カラムごとのユニーク数とユニーク値の表示**
    for col, values in train.iteritems():
        num_uniques = values.nunique()
        print ('{name}: {num_unique}'.format(name=col, num_unique=num_uniques))
        print (values.unique())
        print ('\n')
# 特徴量生成


## dummy化 one-hot-encoding , one hot

カテゴリカラムをonehot化する（元のカラムは削除する）

    df = pd.concat([df, pd.get_dummies(df['category'])], axis=1).drop('category', axis=1)

    # 分割後のからｍカラムにprefixを付けたい（接頭語）
    pd.concat([df, pd.get_dummies(df['early_late_month'], prefix='early_late_month')], axis=1).drop('early_late_month', axis=1)

複数要素を分割して列としてdummy化する

    import pandas as pd
    d = {'col1': ['a,b', 'b', 'c,d', 'a,c'], 'col2': [3, 4, 5, 6]}

    df = pd.DataFrame(d)
    df['col1'].str.get_dummies(sep=',')

    df_sample = df.sample(n=100, random_state=0)
    df_sample.dropna(how='any', axis=0, inplace=True)

# リスト型をdummy化

    pd.concat([df_sample['bk_uuid'], df_sample['category_id'].str.get_dummies(sep=',').astype(dtype=int)], axis=1).sort_index()

# csv形式をdummy化

    df_sample['category_id'] = df_sample['category_id'].str.split(',')
    pd.concat([df_sample['bk_uuid'], pd.get_dummies(df_sample['category_id'].apply(pd.Series).stack()).groupby(level=0).apply(sum).astype(dtype=int)], axis=1).sort_index()


## 時系列データ

rollingの第1引数としてwindowサイズを指定


    # 移動平均
    df.rolling(5).mean()

    df.rolling(3).sum()

    # min_periods 最小期間（min_periods=1を指定するとwindow=3以下の場合であっても1つデータがあれば計算される）
    s.rolling(3, min_periods=1).sum()

    # 行数を変えずに移動平均
    dt[["id", lag_col]].groupby("id")[lag_col].transform(lambda x : x.rolling(win).mean())


## Label Encoding
    # categoryに変換後、cat.codes
    df = pd.DataFrame(d)
    df['city'] = df['city'].astype('category')
    df.dtypes

    df['label_enc'] = df['city'].cat.codes


## Count Encoding
    # method 1
    count_mean = df.groupby('city').target.count()
    df['count_enc'] = df['city'].map(count_mean)

    # method 2
    df['count_enc'] = df.groupby('city')['target'].transform('count')


## LabelCount (Count Rank) Encoding

カテゴリ変数の出現回数が多い順に順位づけ

    count_rank = df.groupby('city')['target'].count().rank(ascending=False)
    df['count_rank'] = df['city'].map(count_rank)


## Target Encoding

Mean EncodingやLikelihood Encodingとも呼ばれる手法
目的変数（Target）に対してカテゴリ変数の処理をおこなう

目的変数が２値のみをとるブーリアン型であればカテゴリ毎の確率を、
目的変数が数値であればカテゴリ毎の平均を取るというもの。

ただし、データセットが学習データとテストデータに分かれている場合、
テストデータの目的変数は未知であるため、演算は学習データに対しておこない、得られた特徴量をテストデータ内のカテゴリへ適用する。
この部分に注意しないと、Cross Validation をおこなったときにデータがリークすることになってしまう。

    # method 1
    target_mean = df.groupby('city').target.mean()
    df['target_enc'] = df['city'].map(target_mean)

    # method 2
    df['target_enc'] = df.groupby('city')['target'].transform('mean')


## One hot encoding
    oh_enc = pd.get_dummies(df['city'], prefix='gender')
    df = pd.concat([df, oh_enc], axis=1)


## Frequency Encoding
    for col in cat_cols:
        freq_encoding = train[col].value_counts()
        # ラベルの出現回数で置換
        train[col] = train[col].map(freq_encoding)
        test[col] = test[col].map(freq_encoding)



# パフォーマンス

行列の計算

- Series として列を取り出し ベクトルとして行うほうが格段に速い。（df['y'] + df['z']）
- np.vectorizeを使うのも早い
    - Pythonの関数を配列に適用できるように変換する関数
- listにしてからappendして最後にDataFrame化する
- DataFrameのままならmapが早い
- 遅い理由
    - 行に対するループは遅い
        - pandas.DataFrame は列ごとに異なる型を持つことができる
        - DataFrame は内部的に 同じ型の列をまとめて np.ndarray として保持している（列ごとに連続したデータを持つ）
    - DataFrame.iterrowsは遅い
        - DataFrame.iterrows でのループの際には 異なる型を持つ列の値を Series として取り出すため、そのインスタンス化にも時間がかかる。
    - DataFrame.applyは遅い
        - このメソッドでは Python の関数を繰り返し呼び出すためのコストがかかる。
        - apply は利便性を重視したメソッドのため、パフォーマンスを気にする場合は避けたほうがよい。

# np.vectorize

    def py_sigmoid(x):
        return 1/ (1+math.exp(-x))
    vsigmoid = np.vectorize(py_sigmoid)
    vsigmoid(data))

numpy配列に一度変換し、処理を行い、またpandasに戻したほうがはやいこともある

    import numpy as np

    data_np = np.asarray(data)
    for row in data_np:
        pass
    data = pd.DataFrame(data_np)


集計や結合

- DataFrame.groupby や  DataFrame.merge など、データの値をキーにして行われる処理はobject 型を避ける
- キーとなる値を カテゴリ型 ( pd.Categorical ) に変換する
- カテゴリ型は
    - カテゴリのラベル (pd.Categorical.categories ) と ラベルの位置 (pd.Categorical.codes ) を分けて扱う
    - 内部処理は int 型で保存されたラベルの位置について行われるため、object に対する処理と比較すると速くなる。
- カテゴリ型ではなくてもカテゴリ型にしたほうが早い
    - Documentには「Categoricalを使用すると、繰り返される値が多いobject-dtype列を効率的に格納できます。」とある
        - [pandas.DataFrame.memory_usage — pandas 1.0.3ドキュメント](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.memory_usage.html#pandas.DataFrame.memory_usage)
- index をキーにする場合
    - カテゴリ型にする
    - ユニークかつソートしておく
- 欠損値 ( NaN ) が無いほうが速い
- object 型内での型の混在 (文字列と数値が混ざっている) していると遅い
    df['z'] = df['z'].astype('category')
    %timeit df.groupby('z').mean()


pd.concatは

__getattr__のオーバーヘッドについて
pandas.Series型がデータサイズ分作成される

    for idx,row in df.iterrows():
        row.a # 27秒

Seriesの生成を避ける

    for idx in range(df.shape[0]):  # shapeのところ.indexの方がスマートかも
        df.a.iloc[idx] # 10秒

__getattr__ のオーバーヘッドを減らす（先にdf.a取得しておく）

    df_a = df.a
    for idx in range(df.shape[0]):
        df_a.iloc[idx] # 6.4秒

valuesでnumpy array型にし、かつ__getattr__を減らす

    a = df.a.values
    for idx in range(df.shape[0]):
        a[idx] # 0.09秒( 90ms)


計算結果を列として追加するならmapメソッド

    def create_house_age_class(age):
            if age < LOWER_SPLIT:
                return AGE_LABEL_YOUNG
            elif (LOWER_SPLIT <= age) and (age < UPPER_SPLIT):
                return AGE_LABEL_MIDDLE
            else:
                return AGE_LABEL_OLD

    _data = data.copy()
    _data.loc[:, 'HousingAgeClass'] = _data.HouseAge.map(create_house_age_class)

DASK

    import dask.dataframe as dd
    reader = dd.read_csv('sample.csv', encoding='utf-8', header=None)
    print(reader.compute())

# DataFrameのインデックス同士の比較

    train[train.index.isin(train_mag[train_mag.isnull().any(axis=1)].index)].reset_index().sum(axis=1)



reset_index()のエラー
TypeError: cannot insert an item into a CategoricalIndex that is not already an existing category
grouped.columns.astype('str')で型を変えるとOK
[pandas-dev/pandas#19136](https://github.com/pandas-dev/pandas/issues/19136)

    dfs=pd.DataFrame({'Year': np.random.randint(2000,2017,10000),
    'Month': np.random.randint(1,12,10000),
    'Data': np.random.randint(0,100,10000)})
    grouped=dfs.groupby(['Year','Month', pd.cut(dfs.Data, range(0,100,10))]).size().unstack()
    grouped
    # This doesn't work:

    grouped.reset_index() #returns TypeError: unorderable types: int() < str()
    # # This works:
    grouped.columns=grouped.columns.astype('str')
    grouped.reset_index()

# DataFrameの列全ての文字列を結合する
# astype(str) は、数値と文字列が混じっているとcatできないため統一させている
df[['CATEGORY_PATH3', 'SITE_PROFILE']].astype(str).apply(lambda r: r.str.cat(sep='_'), axis=1)



# メモリ

pandasのメモリの見方について
[PandasのDataFrameのメモリ使用量を見る | mwSoft](http://www.mwsoft.jp/programming/numpy/dataframe_memory.html)

最終的にはこれで見れる

    df.info(memory_usage='deep')

こちらは上と同じだがサイズのみ返す

    df.memory_usage(deep=True).sum() / 1024**2
    sys.getsizeofの返す値も上と同じ

※間違っているかも ← あっている（下のように'df'と指定すると文字列として使用メモリを返すのでdfを渡す必要がある）

    sys.getsizeof('df')/1024**2

csr_matrixのメモリ確認用

    def get_size_of_csr(csr):
    return csr.data.nbytes + csr.indices.nbytes + csr.indptr.nbytes
    get_size_of_csr(df_final_sparse) / 1024**2


変数の確認

    %whos

ベスト５

    import sys
    import pandas as pd
    from scipy.sparse import csr_matrix
    def get_size_of_csr(csr):
        return csr.data.nbytes + csr.indices.nbytes + csr.indptr.nbytes
    def get_memory_use(local_var_list, top_n=5):
        columns = ['Variable Name', 'type', 'Memory(MB)']
        memory_df = pd.DataFrame(columns=columns)

        for var_name in [s for s in local_var_list if not s.startswith('_')]:
            d_type = type(eval(var_name))
            if d_type == csr_matrix:
                mem = get_size_of_csr(eval(var_name))/1024**2
            else:
                mem = sys.getsizeof(eval(var_name))/1024**2
            memory_df = memory_df.append(pd.DataFrame([[var_name, d_type, mem]], columns=columns))
        display(memory_df.sort_values('Memory(MB)', ascending=False).head(top_n))
    get_memory_use(dir(), top_n=5)


## メモリ削減
    import gc
    del df
    gc.collect()

参考：[https://qiita.com/nannoki/items/1466779987b68c4f4bf9](https://qiita.com/nannoki/items/1466779987b68c4f4bf9)

    from itertools import chain
    import sys
    from collections import deque
    def compute_object_size(o, handlers={}):
        dict_handler = lambda d: chain.from_iterable(d.items())
        all_handlers = {tuple: iter,
                        list: iter,
                        deque: iter,
                        dict: dict_handler,
                        set: iter,
                        frozenset: iter,
                       }
        all_handlers.update(handlers)     # user handlers take precedence
        seen = set()                      # track which object id's have already been seen
        default_size = sys.getsizeof(0)       # estimate sizeof object without __sizeof__
        def sizeof(o):
            if id(o) in seen:       # do not double count the same object
                return 0
            seen.add(id(o))
            s = sys.getsizeof(o, default_size)
            for typ, handler in all_handlers.items():
                if isinstance(o, typ):
                    s += sum(map(sizeof, handler(o)))
                    break
            return s
        return sizeof(o)
    def show_objects_size(threshold, unit=2):
        disp_unit = {0: 'bites', 1: 'KB', 2: 'MB', 3: 'GB'}
        globals_copy = globals().copy()
        for object_name in globals_copy.keys():
            size = compute_object_size(eval(object_name))
            if size > threshold:
                print('{:<15}{:.3f} {}'.format(object_name, size, disp_unit[unit]))
    # 100MB超のオブジェクト一覧を表示する
    show_objects_size(100)

メモリ削減

    def reduce_mem_usage(df):
        """ iterate through all the columns of a dataframe and modify the data type
            to reduce memory usage.
        """
        start_mem = df.memory_usage().sum() / 1024**2
        print('Memory usage of dataframe is {:.2f} MB'.format(start_mem))
        for col in df.columns:
            col_type = df[col].dtype
            if col_type != object:
                c_min = df[col].min()
                c_max = df[col].max()
                if str(col_type)[:3] == 'int':
                    if c_min > np.iinfo(np.int8).min and c_max < np.iinfo(np.int8).max:
                        df[col] = df[col].astype(np.int8)
                    elif c_min > np.iinfo(np.int16).min and c_max < np.iinfo(np.int16).max:
                        df[col] = df[col].astype(np.int16)
                    elif c_min > np.iinfo(np.int32).min and c_max < np.iinfo(np.int32).max:
                        df[col] = df[col].astype(np.int32)
                    elif c_min > np.iinfo(np.int64).min and c_max < np.iinfo(np.int64).max:
                        df[col] = df[col].astype(np.int64)
                else:
                    if c_min > np.finfo(np.float16).min and c_max < np.finfo(np.float16).max:
                        df[col] = df[col].astype(np.float16)
                    elif c_min > np.finfo(np.float32).min and c_max < np.finfo(np.float32).max:
                        df[col] = df[col].astype(np.float32)
                    else:
                        df[col] = df[col].astype(np.float64)
            else:
                df[col] = df[col].astype('category')
        end_mem = df.memory_usage().sum() / 1024**2
        print('Memory usage after optimization is: {:.2f} MB'.format(end_mem))
        print('Decreased by {:.1f}%'.format(100 * (start_mem - end_mem) / start_mem))
        return df

[関数 – 少し高度な関数の使い方【Python入門22】 | Pythonと機械学習の入門サイト](https://ruby-de-free.net/wp/functions-using-somewhat-advanced-functions/)




# **特徴量エンジニアリング**
## **日付・時間を処理**
    train['datetime'] = pd.to_datetime(train['datetime']) # dtype を datetime64 に変換
    train['year'] = train['datetime'].dt.year
    train['month'] = train['datetime'].dt.month
    train['day'] = train['datetime'].dt.day
    train['dayofweek'] = train['datetime'].dt.dayofweek
    train['hour'] = train['datetime'].dt.hour
    train['minute'] = train['datetime'].dt.minute
    train['second'] = train['datetime'].dt.second
## **特定の文字を含んだカラム名のリストを得る**
    # 'target' という文字を含んだカラムを取得
    cols = [c for c in train.columns if 'target' in str(c)]
## **json 形式のカラムを複数のカラムに展開する**
    df_json = json_normalize(train['json_col'].apply(lambda x: json.loads(x)))
## **複数の情報を含んだカラムを分割する**

Android 6.0.1、iOS 11.4.0 といった OSとバージョン情報を複数含んだカラムを分割する。

    # アンダーバーで分割
    train['OS'] = train['OS_VERSION'].str.split('_', expand=True)[0]
    train['VERSION'] = train['OS_VERSION'].str.split('_', expand=True)[1]
## **Count Encoding**

カテゴリ変数の列で各カテゴリの出現回数をカウント。ここでは、train と test における出現回数を合計した特徴量を生成。カテゴリ値の人気度を測定しているようなものと解釈する。
参考：[https://blog.datarobot.com/jp/automatedfeatureengineering](https://blog.datarobot.com/jp/automatedfeatureengineering)

    train['col_name_count'] = train['col_name'].map(pd.concat([train['col_name'], test['col_name']], ignore_index=True).value_counts(dropna=False))
    test['col_name_count'] = test['col_name'].map(pd.concat([train['col_name'], test['col_name']], ignore_index=True).value_counts(dropna=False))
## **clipping**
    # 99%
    upperbound, lowerbound = np.percentile(train['col_name'], [1, 99])
    train['col_name_clipped'] = np.clip(train['col_name'], upperbound, lowerbound)
## **normalize**
    train['col_name_nomalize'] = (train['col_name'] - train['col_name'].mean() ) / train['col_name'].std()
## **カテゴリ変数のみ Label Eoncoding する**
    from sklearn.preprocessing import LabelEncoder

    for col in train.columns:
        if train[col].dtype == 'object':
            le = LabelEncoder()
            le.fit(list(train[col].astype(str).values) + list(test[col].astype(str).values))
            train[col] = le.transform(list(train[col].astype(str).values))
            test[col] = le.transform(list(test[col].astype(str).values))
## **行のNAN数を新しい特徴量に**
    train['number_of_NAN'] = train.isna().sum(axis=1).astype(np.int8)
## **ビニング処理**

ビンに含まれる個数を指定

    df['col_name_qcut_10'] = pd.qcut(df['col_name'], 10)
## **test データに無い場合１、ある場合は０にする特徴量**
    train['col_name_check'] = np.where(train['col_name'].isin(test['col_name']), 1, 0)
    # test の場合は逆
    test['col_name_check']  = np.where(test['col_name'].isin(train['col_name']), 1, 0)
## **全て０のカラムを作成する**
    train["col_name_zero"] = np.zeros(train.shape[0])
## **NaN とそれ以外の値の特徴量を作成する**
    train['col_name_nan'] = np.where(train['col_name'].isna(), 1, 0)
## **nan 数の同じカラムごとにグループ化**
    nans_groups = {}
    nans = pd.concat([train, test]).isna()

    for col in train.columns:
        cur_group = nans[col].sum()
        if cur_group > 0:
            try:
                nans_groups[cur_group].append(col)
            except:
                nans_groups[cur_group] = [col]

    for n_group, n_members in nans_groups.items():
        print(n_group, len(n_members), n_members)
## **Aggregated Features**

カテゴリのグループごとに aggregation する。例えば、同じip, os, deviceの総クリック数を計算するなど。参考：[Kaggle Masterに学ぶ実践的機械学習[Kaggle TalkingData Competition編]](https://qiita.com/keitakurita/items/f10e658843930b888814#aggregated-features)

    agg_types = ['max', 'min', 'sum', 'mean', 'std', 'count']
    for agg_type in agg_types:
        new_col_name = cat_col + '_' + agg_col + '_' + agg_type
        temp = pd.concat([train[[cat_col, agg_col]], test[[cat_col, agg_col]]])
        temp = temp.groupby([cat_col])[agg_col].agg([agg_type]).reset_index().rename(columns={agg_type: new_col_name})
        temp.index = list(temp[cat_col])
        temp = temp[new_col_name].to_dict()
        train[new_col_name] = train[cat_col].map(temp)
        test[new_col_name]  = test[cat_col].map(temp)
## **numeric feature を 0以上にシフトする**
    for col in train.columns:
        if not ((np.str(train[col].dtype)=='category')|(train[col].dtype=='object')):
            min = np.min((train[col].min(), test[col].min()))
            train[col] -= np.float32(min)
            test[col] -= np.float32(min)
## **numeric feature の 欠損値を -1 で埋める**
    for col in train.columns:
        if not ((np.str(train[col].dtype)=='category')|(train[col].dtype=='object')):
            train[col].fillna(-1, inplace=True)
            test[col].fillna(-1, inplace=True)
## **frequency encoding**

カテゴリ変数の出現回数で変数を置き換える。

    def freq_enc(train, test, cols):
        for col in cols:
            df = pd.concat([train[col], test[col]])
            vc = df.value_counts(dropna=True, normalize=True).to_dict()
            vc[-1] = -1 # 欠損値を -1 で埋める場合
            new_col = col + '_freq_enc'
            train[new_col] = train[col].map(vc)
            train[new_col] = train[new_col].astype('float32')
            test[new_col] = test[col].map(vc)
            test[new_col] = test[new_col].astype('float32')

    freq_enc(train, test, feature_list)
## **特徴量同士を結合した特徴量を作成し Label Encoding**
    def conb_enc(col1, col2, train, test):
        nm = col1 + '_' + col2
        train[new_col] = train[col1].astype(str) + '_' + train[col2].astype(str)
        test[new_col] = test[col1].astype(str) + '_' + test[col2].astype(str)
        le = LabelEncoder()
        le.fit(list(train[new_col].astype(str).values) + list(test[new_col].astype(str).values))
        train[new_col] = le.transform(list(train[new_col].astype(str).values))
        test[new_col] = le.transform(list(test[new_col].astype(str).values))
## **あるカラム群の欠損の数の合計を特徴量にする**
    train['missing'] = train[col_list].isna().sum(axis=1).astype('int16')
    test['missing'] = test[col_list].isna().sum(axis=1).astype('int16')


# **特徴選択**
## **constant なカラムを抜き出す**

90%以上、同じ値のカラムを抜き出す

    def get_constant_cols(df):
        constant_cols = [col for col in df.columns if df[col].value_counts(dropna=False, normalize=True).values[0] > 0.9]
        return constant_cols

    cols = get_constant_cols(train)
## **不要なカラムを落とす**
- 値が一つしかないカラム
- null が多いカラム
- ほとんど同じ値のカラム
    one_value_cols = [col for col in train.columns if train[col].nunique() <= 1]
    one_value_cols_test = [col for col in test.columns if test[col].nunique() <= 1]

    many_null_cols = [col for col in train.columns if train[col].isnull().sum() / train.shape[0] > 0.9]
    many_null_cols_test = [col for col in test.columns if test[col].isnull().sum() / test.shape[0] > 0.9]

    big_top_value_cols = [col for col in train.columns if train[col].value_counts(dropna=False, normalize=True).values[0] > 0.9]
    big_top_value_cols_test = [col for col in test.columns if test[col].value_counts(dropna=False, normalize=True).values[0] > 0.9]

    cols_to_drop = list(set(many_null_cols + many_null_cols_test + big_top_value_cols + big_top_value_cols_test + one_value_cols+ one_value_cols_test))

    train.drop(cols_to_drop, axis=1, inplace=True)
    test.drop(cols_to_drop, axis=1, inplace=True)
## **再帰的特徴量選択**

まったく特徴量を使わないところから、ある基準が満たされるまで1つずつ重要度が高い特徴量を加えていく、もしくは、すべての特徴量を使う状態から1つずつ特徴量を取り除いていくという操作を繰り返すことで特徴量を選択する。
RFE(Recursive Feature Elimination; 再帰的特徴量削減)は、すべての特徴量から開始してモデルを作り、そのモデルで最も重要度が低い特徴量を削除する。そしてまたモデルを作り、最も重要度が低い特徴量を削除する。この過程を事前に定めた数の特徴量になるまで繰り返す、という手法である。
引数には estimator、および特徴量の数 n_features_to_select（残す特徴量数） を指定する。
(特徴量数 - n_features_to_select) 回、モデル作成=>特徴量削除 が行われるので処理時間がかかる。

----------

[sklearn.feature_selection.RFE — scikit-learn 0.22.1 documentation](https://scikit-learn.org/stable/modules/generated/sklearn.feature_selection.RFE.html)

    from sklearn.feature_selection import RFE
    from sklearn.ensemble import RandomForestClassifier

    X_train = train.drop('target', axis=1)
    y_train = train['target']

    select = RFE(RandomForestClassifier(n_estimators=100, random_state=42), n_features_to_select=40)
    select.fit(X_train, y_train)

    X_train_rfe = select.transform(X_train)
    X_test_rfe = select.transform(test)
## **コルモゴロフ-スミルノフ検定を利用した特徴量選択**
    from scipy.stats import ks_2samp
    list_p_value =[]

    for i in tqdm(train.columns):
        list_p_value.append(ks_2samp(test[i], train[i])[1])

    Se = pd.Series(list_p_value, index=train.columns).sort_values()
    list_discarded = list(Se[Se < .1].index)

参考：[https://www.kaggle.com/c/elo-merchant-category-recommendation/discussion/77537](https://www.kaggle.com/c/elo-merchant-category-recommendation/discussion/77537)

## **Null Importance による特徴量選択**

LightGBM などの学習器における feature importance で、上位に来た特徴量の中にノイズになっているものが含まれていることがある。そこで正しい目的変数で学習した結果の feature importance と目的変数を shuffle したデータを用いて学習した結果の feature importance を比較することでノイズになっている特徴量を抽出する。

    def get_feature_importances(X, shuffle, seed=None):
        cols_to_drop = ['col_to_drop_1','col_to_drop_2']
        categoricals = ['cat_col']
        y = X['target']
        X = X.drop(cols_to_drop, axis=1)

        if shuffle:
            y = np.random.permutation(y)

        train = lgb.Dataset(X, y, free_raw_data=False, silent=True)

        lgb_params = {
            'objective': 'binary',
            'boosting_type': 'rf',
            'subsample': 0.623,
            'colsample_bytree': 0.7,
            'num_leaves': 127,
            'max_depth': 8,
            'seed': 42,
            'bagging_freq': 1,
            'n_jobs': 4
        }

        clf = lgb.train(params=lgb_params, train_set=train, num_boost_round=200, categorical_feature=categoricals)

        df_importance = pd.DataFrame()
        df_importance["feature"] = list(X.columns)
        df_importance["importance"] = clf.feature_importance()
        df_importance['train_score'] = roc_auc_score(y, clf.predict(X)) # 二値分類の場合

        return df_importance

    def display_distributions(df_actual_importance, df_null_importance, feature):
        actual_imp = df_actual_importance.query(f"feature == '{feature}'")["importance"].mean()
        null_imp = df_null_importance.query(f"feature == '{feature}'")["importance"]

        fig, ax = plt.subplots(1, 1, figsize=(6, 4))
        a = ax.hist(null_imp, label="Null importances")
        ax.vlines(x=actual_imp, ymin=0, ymax=np.max(a[0]), color='r', linewidth=10, label='Real Target')
        ax.legend(loc="upper right")
        ax.set_title(f"Importance of {feature.upper()}", fontweight='bold')
        plt.xlabel(f"Null Importance Distribution for {feature.upper()}")
        plt.ylabel("Importance")
        plt.show()

    # 実際の目的変数で学習し、feature importance の DataFrame を作成
    df_actual_importance = get_feature_importances(X=reduce_train, shuffle=False)

    # シャッフルした目的変数で学習し、feature importance の DataFrame を作成
    nb_runs = 100
    df_null_importance = pd.DataFrame()
    for i in range(nb_runs):
        df_importance = get_feature_importances(X=reduce_train, shuffle=True)
        df_importance["run"] = i + 1
        df_null_importance = pd.concat([df_null_importance, df_importance])

    # 実データにおいて特徴量の重要度が高かった上位5位を表示
    for feature in actual_imp_df\["feature"\][:5]:
        display_distributions(df_actual_importance, df_null_importance, feature)

    # 閾値を設定
    THRESHOLD = 80

    # 閾値以下(ノイズ)の特徴量を取得
    not_important_features = []
    for feature in df_actual_importance["feature"]:
        actual_value = df_actual_importance.query(f"feature=='{feature}'")["importance_split"].values
        null_value = df_null_importance.query(f"feature=='{feature}'")["importance_split"].values
        percentage = (null_value < actual_value).sum() / null_value.size * 100
        if percentage < THRESHOLD:
            not_important_features.append(feature)

**参考**

- [Feature Selection with Null Importances](https://www.kaggle.com/ogrellier/feature-selection-with-null-importances/comments)
- [Null Importanceを用いた特徴量選定](https://qiita.com/KenkenGoda/items/1d6ede5d492d1a9dc3c9)
