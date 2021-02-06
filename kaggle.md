# kaggle
[GitHub-Kaggle / kaggle-api：公式のKaggle API](https://github.com/Kaggle/kaggle-api)

# API準備
```py
# insatall
!pip install kaggle

# auth google kaggle
from googleapiclient.discovery import build
import io, os
from googleapiclient.http import MediaIoBaseDownload
from google.colab import auth

auth.authenticate_user()

drive_service = build('drive', 'v3')
results = drive_service.files().list(q="name = 'kaggle.json'", fields="files(id)").execute()
kaggle_api_key = results.get('files', [])

filename = "/root/.kaggle/kaggle.json"
os.makedirs(os.path.dirname(filename), exist_ok=True)

request = drive_service.files().get_media(fileId=kaggle_api_key\[0\]['id'])
fh = io.FileIO(filename, 'wb')
downloader = MediaIoBaseDownload(fh, request)
done = False
while done is False:
    status, done = downloader.next_chunk()
    print("Download %d%%." % int(status.progress() * 100))
os.chmod(filename, 600)

# dowonload
ref = 'kashnitsky/mlcourse'
dataset_download_dir = '/kaggle/input/'# + ref
os.makedirs(dataset_download_dir, exist_ok=True)
# コンペ
# !kaggle competitions download -c $ref -p $dataset_download_dir --unzip
# データセット
!kaggle datasets download $ref -p $dataset_download_dir --unzip
# !unzip -d $dataset_download_dir $dataset_download_dir'/*.zip'
# current dir

# --unzipオプションがあるので不要か
# !unzip -d $dataset_download_dir $dataset_download_dir'/*.zip'

# current dir
os.chdir(dataset_download_dir)
```

download時のrefを探す

dataset 検索
```py
!kaggle datasets list -s 'mlcourse'
```

competitions list
```py
!kaggle competitions list
```



- CV
- trainとtestに単純に分割したら、testに対してチューニングするためオーバーフィットされる
- trainをtrainとvalidに分割し、チューニングはvalidで、最終テストをtestで行う
- さらに、交差検証（CV)はtrain, validの分割位置を変えて、K個のtrain, validのデータセットを作り、それぞれ学習と検証を行う
- →複数の検証結果を平均などして評価するため、たまたま１つのデータセットで性能が良いモデル（オーバーフィット）であることを避けれれる
-
- 回帰タスクの場合、KFoldを使うことが多いが、targetのbinで層化KFoldが有効なケースもある
- 分類の場合は、層化KFoldで、targetのクラスの割合を保ちつつKFoldを行えるので、Foldごとに特定のクラスに強いモデルが学習されたりしない
-
-  train / test の関係性と cv の各 fold での学習・検証データの関係性が出来る限り似た関係性であることが望ましい


- train testのカテゴリ変数の一致具合
- testにのみ存在するカテゴリ値が存在する場合、trainデータでいくら学習しても予測できない
- 別視点だと、trainデータでいくら、モデルをチューニングしても、未知データ（test）を予測することはできない（trainにオーバーフィッティング）
- カテゴリ変数をベン図で可視化するのがわかりやす
```py
from matplotlib_venn import venn2

columns = test.columns
n_figs = len(columns)
n_cols = 4
n_rows = n_figs // n_cols + 1

fig, axes = plt.subplots(figsize=(n_cols * 3, n_rows * 3), ncols=n_cols, nrows=n_rows)

for c, ax in zip(columns, axes.ravel()):
    venn2(
        subsets=(set(train[c].unique()), set(test[c].unique())),
        set_labels=('Train', 'Test'),
        ax=ax
    )
    ax.set_title(c)

fig.tight_layout()
```

# RMSLE 最適化
- RMSLEを直接最適化しようとすると微分すると log が残る
    - 予測値が -1 以下 で loss が nan になる可能性あり

```py
import numpy as np
t_d = np.log1p(t)  # ラベルtにlog1p -> log(t+1)
y_d = np.expm1(y)  # 予測結果にexpm1 しもとに戻す
```

# EDA

Sweetviz https://github.com/fbdesignpro/sweetviz

trainとtestの比較をしてくれる


# CV

[scikit-learnでの相互検証動作の視覚化— scikit-learn0.23.2ドキュメント](https://scikit-learn.org/stable/auto_examples/model_selection/plot_cv_indices.html#sphx-glr-auto-examples-model-selection-plot-cv-indices-py)



# Feature Enginearing

カテゴリ変数で、trainとtestでカテゴリ値が被っていないような場合

単純にその変数をlabel-encodeやcount-encodeして学習してもオーバーフィッティングになる（CVとLBの乖離）
train test合わせて全体のデータから、カテゴリ変数をキーにして、他の変数をピボットでカウントした情報を加える
```py
plat_pivot = whole_df.pivot_table(index='Publisher',
    columns='Platform',
    values='Name',
    aggfunc='count').reset_index()
```


## PCAで次元削減
- onehot化やlabel encodeすると特徴量が増える
- 全部の情報を使うと多すぎるので、現状はPCAでデータを削減する
- 学習データに対して特徴量の数が多いとバリアンスが高まり、汎化誤差が大きくなる
- ※PCAする前に標準化が必要
- ※PCAすると説明性が低下する（なくなる

- scikit-learnのdecomposition PCA
- TruncatedSVD
- 疎行列を扱えるなどの理由からか、PCAより TruncatedSVDの方がよく使われているらしい

```py
from sklearn.decomposition import TruncatedSVD
# データは標準化などのスケールを揃える前処理が行われているものとする
# 学習データに基づいてSVDによる変換を定義
svd = TruncatedSVD(n_components=5, random_state=71)
svd.fit(train_x)
# 変換の適用
train_x = svd.transform(train_x)
test_x = svd.transform(test_x)
```
