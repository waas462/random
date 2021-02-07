# EDA


参考
[【随時更新】Kaggleテーブルデータコンペできっと役立つTipsまとめ - ML_BearのKaggleな日常](https://naotaka1128.hatenadiary.jp/entry/kaggle-compe-tips)
[courseraのKaggle講座内容まとめ - Qiita](https://qiita.com/saitosasaki/items/da2a0f6ad2cc5c5ea7a2)

未取り込み
[特徴量選択について - Qiita](https://qiita.com/fhiyo/items/33b295de64f5a6a047c6)
[機械学習 カテゴリーの記事一覧 - 学習する天然ニューラルネット](https://aotamasaki.hatenablog.com/archive/category/%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92)

interpretable Machine Learning
[機械学習と解釈可能性 / Machine Learning and Interpretability - Speaker Deck](https://speakerdeck.com/line_developers/machine-learning-and-interpretability)

目標

- EDAとして一般的にやるべき事がイメージできる
- データの統計的な捉え方が出来るようになる
- アルゴリズムの気持ちが分かるようになる
- 実装力を上げ、効率的な
- lgbなどのclassオーバーライドした時に、fitで何をすべきかとか、形状がどうであるべきなのかとか

TODO

- EDA
    - 疎行列の可視化どうやる？
# 構成

EDA
特徴量生成
Feature selection
　特徴量が多くデータ数が少なく過学習に陥りがちな時
欠損値処理
不均衡データの扱い
モデル
Validation
Ensemble / Stacking



スプレッドシートで手軽にデータを見れるようにしておくと便利
pandas_profileをとりあえず出しておくと便利

    この変数のmissing valueの割合は？とか


テーブルデータの場合

- 各データのpandas_profileをhtml出力
    - 日付は日付型に変換しておくastype
    - submit sample
        - 粒度の確認（この粒度でtrain, testを作る）
    - test
        - train と一致しているか
            - ベン図
    - 前処理後、確認の意味も込めてpandas_profileを別名で出力
    - 欠損値

    - ターゲットの分布

```py
!pip install dtale

import dtale
import dtale.app as dtale_app
dtale_app.USE_COLAB = True

dtale.show(train_input_log,  ignore_duplicate=True)
```

```py
!pip install sweetviz

import sweetviz

# my_report = sweetviz.analyze(train_input_log)
my_report = sweetviz.compare([train_input_log, "Train"], [test_input_log, "Test"])

my_report.show_html("Report.html")
```

## **EDA(Explanatory Data Analysis)**

**全般**

- 与えられたデータを確認
    - 何の単位で予測する問題なのか
    - trainとtestは同じ単位のデータなのか（trainを集約しないといけないケースがある）
    - １つや1人のデータをまずはよく見る
    - どうやってそのデータができたのか
    - カラムの意味を理解
    - データを確認してどんなモデルだと特徴をうまく理解できるか
    - データがどのようにして作成されているのか
    - 目的変数に対して各特徴量の相関があるのか
    - 欠損値、不均衡、数値の取る範囲、
    - 情報量が無い特徴量
    - データクリーニング
- 評価指標の確認
- まずは機械的に一連のチェックをするのが良さそう
    - 各カラムの欠損値確認
    - 外れ値（相関係数に影響する）
-
- EDAのとっかかりがなかったらXGBoostやLightGBMのfeature_importanceをまず見るのでも良い(2)
- targetに影響する特徴量を想像してtodo化しておこう
- 間違ってる列は消すんじゃなくて、間違ってるデータ列だとアルゴリズムに教えよう。(あなたよりアルゴリズムのほうが賢いこともよくあるよ)
- 可視化するとき
    - メインとなる変数（目的変数がそれにあたりそう）と、他の説明変数を１つか２つの組合せで見ていく
    - 基本的には説明変数を順次変えてすべて見てみる
        - 傾向からトレーニングに効きそうな特徴量を想定する
    - 一軸が役に立たないなら二軸に散布図かこう
        - 散布図はTrain/Testが同質なデータか否かもわかるし便利だよ
        - 以下のコードで一発



# EDAライブラリ

[4 Libraries that can perform EDA in one line of python code | by Satyam Kumar | Oct, 2020 | Towards Data Science](https://towardsdatascience.com/4-libraries-that-can-perform-eda-in-one-line-of-python-code-b13938a06ae)

    !pip install autoviz
    import pandas as pd
    from autoviz.AutoViz_Class import AutoViz_Class
    #EDA using Autoviz
    autoviz = AutoViz_Class().AutoViz(path, sep='\t')


    !pip install dtale
    import dtale
    import pandas as pd
    dtale.show(pd.read_csv(path))


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
    profile.to_file("ProfileReport_cci_9client.html")
    HTML(filename='ProfileReport_cci_9client.html')



pd.scatter_matrix(df)


- スパイク見つけるためにヒストグラム書こう (これは絶対)
- 相関係数は線形が前提になっている（2次関数の相関になっている可能性もある）
- 相関係数を算出する前に散布図を確認しよう
- Excelなどで色つけて眺めてみよう
- anonymous featureはリバースエンジニアリングしたらたまに良いことあるよ
    - 誕生日の年とか出てきて役立った例とかあるよ
- 以下の使える表現は覚えよう

df.dtypes, df.info, x.value_counts, x.isnull


- 時系列などの場合は特に意味のある順序にソートして眺めよう(5)

ベースライン設定

    - データを最低限整えたらLGBで

特徴量作成

- モデルが特徴を理解しやすいような特徴空間を用意してあげるイメージ
- 全件データ使う必要がない時、どの程度サンプリングしても問題ないかはどうやって考える？

特徴量選択

- 特徴量が多くデータ数が少ないゆえに過学習になるケース

バリデーション作成
汎化性能をあげる
モデル定義

- 問題に

トレーニング
評価
アンサンブル
**データクリーニングの話**

- 情報量ゼロのカラムを消そう
    - 1種類の値しか入ってないカラム
    - 完全にダブってるカラム
    - 相関係数が1のカラム
    - [参考Kernel](https://www.kaggle.com/yuansun/lb-0-84-for-starters)
- Testでしか新しい値が出てこないカラム消そう
- ラベル変えたら同じになるカラム消そう
    - pd.factorize 使えば楽だよ
- Train/Testでデータ被ってることあるので、念のため理由を推測してみよう。
- 移動平均取ってみたらデータが正常にシャッフルされてないこと気付けるかも？
- プロットしてみるとデータがシャッフルされてるとかもわかるよ
- 線が出現したらその値が頻出とかもよくわかるよ
- カテゴリ変数の列を抜き出す表現は覚えよう

df.select_dtypes(include=['object']).columns

特徴量作成

扱うモデルによって作成する
ツリーベースモデル、NNははっきり分かれる
数値の大きさ
欠損値の有無
変数間の相互作用　非線形
カテゴリ値の扱い

モデルが学習しにくい特徴量を追加してあげる



## **Numerical Data**

**概要**

- 採用するモデルによって前処理の方法は変えよう
- 線形モデルと決定木モデルでは必要なデータが違うよ
- 異なる前処理を行ったモデルを混ぜると効くことがあるよ

**Scaling**

- 決定木はスケーリングは基本的に関係ないよ
- MinMaxScaler ⇔ StandardScaler
    - 線形モデルではどっちでも変わらないよ
    - kNNは結構影響あるかも

**異常値排除 / 各種変換**
**clipping**

- 99%タイルにクリッピングするとか効くよ

upperbound, lowerbound = np.percentile(x, [1, 99])
y = np.clip(x, upperbound, lowerbound)
**rank transformation**

- outliersをしこしこ処理できないときはrankに変換してしまおう
    - kNNやNNはこの変換が効くよ

scipy.stats.rankdata

**log transformation**

- NNに激しく効くよ

np.log(1+x)
np.sqrt(x+2/3)
**Numericalデータの特徴量作成**

- とにかく沢山の特徴量を作ってみよう
    - count, sum, max, min, rolling
    - is_null, is_zero, is_not_zero, over_0.5
    - 行ごとの統計情報をまとめてみよう(9)
        - Nanの数, 0の数, 負の数とか
- 要素の掛け算、割り算とかしよう
    - ◯◯と△△との差
    - カテゴリ内の平均XX
    - 変数間の相互作用は[PolynomialFeatures](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.PolynomialFeatures.html)で計算できる
- 小数点以下だけ取り出した要素とか作ろう
    - 4980円の980円的なやつ
        - 4ドルと3.98ドルは人間心理的にぜんぜん違うよ
    - もちろん切り捨て/切り上げとかも作る価値はあるよ
        - それをカテゴリカル変数として扱っても良いかもね(9)
- 木モデルでは以下のデータ処理が必要
    - 積み上げの値を普通の値に戻す (線形モデルでは不要)
    - 和、差、積など取るのは重要だから頑張ろう
        - 木モデルはカラム間の処理を頑張ると良いよ
            - 差や比率を直接表現できないため
            - Owen曰く(8)
                - GBM only APPROXIMATE interactions and non-linear transformations.
                - Strong interactions benefit from being explicitly defined.
- データによってはNumerical→Categorical変換するのもあり
    - 例: 年齢層(30代前半)とか
- 網羅的にやるよりもビジネス的に意味がある演算をすると効率いい
    - [HomeCreditでの例](https://www.slideshare.net/mlm_kansai/kaggle-138546659)
        - 借入額と毎月の返済額の比
        - 利用限度額と月々の利用額の比
        - 支払い予定日と実際の支払日の日数差
        - 借入額と頭金の比率 など
    - [Avitoでの例](https://www.slideshare.net/mlm_kansai/kaggle-138546659)
        - 同一カテゴリ内の平均価格と自分の価格との差/比
            - 例: iPhoneの中では安い
        - 商品名の中の名詞でグループ化した価格平均を使った特徴量
    - [Kaggle Days Paris by CPMP](https://www.youtube.com/watch?v=fH_FiquKhiI&feature=youtu.be)
        - 2Sigma Appt Rental: apartmentの説明文から相場を予測 → その相場予測と家賃との差を特徴量にした
        - TalkingData: アプリDLしたらもう広告踏まないよね？ → 同じ端末から同じADへの次のクリックへの時間を予測し、実際の時間との差を (ry
    - [Avito 9th](https://www.slideshare.net/JinZhan/kaggle-avito-demand-prediction-challenge-9th-place-solution-124500050)
- ビン詰めすると効くこともある(9)
    - Testの数値範囲がTrainと外れているときとか
## **Categorical / Ordinal Data**

**Encodings**

- LabelEncoding
    - 木モデルに有効
        - 線形モデルの場合はOnHotEncodingが追加で必要
        - 木モデルはOnHotEncodingは不要なことが多い
            - 遅くなるのでむしろ不利かもしれない
    - S席、A席、みたいなやつは数字にしてあげると効くこともある
- FrequencyEncoding
    - 出現比率でエンコーディングする
    - 線形モデルだけではなく、木モデルにも有効
    - 異常値に弱い(9)
    - logを取ると効くこともある(9)
- TargetEncoding
    - 強力だけどdata leakageには注意しよう
    - leakageを防ぐ工夫は[H2O.aiの人のスライド](https://www.slideshare.net/0xdata/feature-engineering-83511751)が詳しい
        - Smoothing: [日本語記事](https://mikebird28.hatenablog.jp/entry/2018/06/14/172132)もある
        - leave-one-out
        - Weight of Evidence
    - その他の工夫
        - out-of-fold ([Kaggle Meetup #4 LT by Jackさん](https://speakerdeck.com/rsakata/kaggle-meetup-number-4-lightning-talks?slide=9))
        - random noise を加える ([Port Seguro Kernel](https://www.kaggle.com/ogrellier/python-target-encoding-for-categorical-features))
    - 時系列など過学習が気になる際はTargetそのものではなく、重要特徴量にたいして行うと選択肢もアリ([HomeCredit 2nd](https://speakerdeck.com/hoxomaxwell/home-credit-default-risk-2nd-place-solutions?slide=17))
    - 基本的にLeakが気になるケースでは初心者は用いないほうが良い(2)
    - 時系列の場合
        - CatBoost で過去全体のtargetの平均を用いるという方法もある([kaggler-ja mamasさん](https://kaggler-ja.slack.com/archives/C6RV8M3DG/p1556006652228000))
            - [Column descriptions](https://catboost.ai/docs/concepts/input-data_column-descfile.html)で Timestamp を指定し、
            - さらに[training parameters](https://catboost.ai/docs/concepts/python-reference_parameters-list.html)の has_time を指定する。
        - 過去N月のtarget平均を使うのも良い

**Tips**

- 複数のカテゴリカル変数をくっつけて使うこともある
    - 性別☓客室等数とか
    - 線形モデルで有効
    - 多くの掛け合わせをすることを恐れるな
        - OwenはAmazonCompetitionで7掛け合わせ変数を使った(8)
        - アホみたいな掛け合わせが効くことあるからweird interactionも試してみろ(9)
## **Datetime / Coordinates Data**

**Date and time**

- 日付は重要なこと多いから徹底して特徴量作成しよう
    - 通常のDatetimeに加えて、◯◯から△分、とかも使える
    - 同様に、◯した日と△した日の差、とかも
    - 午前/午後/深夜とか、平日/休日/祝日/祝前日、とか
    - 国民の祝日、大きなスポーツイベント、第一土曜日とか(9)
    - 重要な日付から近いものの重みを大きくしてみよう
        - 逆に遠いものは減衰させて重みを小さくしてみよう
- 曜日・時刻・(月の)日付などの周期性のある特徴量を円周上に配置し、cos・sinに分解して、循環連続性を表現するのも効果的(2)(9)

**Coordinates(座標/地理) Data**

- 重要なランドマークや都市からの距離とか
- 周辺地価とかの有効なデータの追加とかも効く
- 都市はgeographic feature作ったりいろいろやってみよう
- 近くに何個家があるか、学校があるか、など近くの情報も使おう
- GPS座標、郵便番号の住所への付与とかもやってみたら良いかもね(9)
- ニセの空間情報に気づけ(9)
    - 不可能な速度での移動
    - 今いる場所と違う場所での出費
## **その他 特徴量作成 Tips**

**次元削減系**

- PCAよりNMFのほうが木モデルに使いやすい次元縮約をしてくれる
- PCAが効く理由
    - XGBoostなどの決定木系アルゴリズムが軸に斜めの表現を不得手(表現できないわけではないが、PCAで回転した方が効率的)としていることに起因するとのこと(2)
- トピックモデル(LDA等)の活用を検討しよう(4)

**時系列データ**

- 基本的には直近のもので学習させたほうが良いが、1年前などに特徴的な周期性がないかは確認すべき(5)
- 時系列で重み付けしたカウントなどは有効な特徴量になり得る(5)
- 「費用」は先週の費用、先月の費用、昨年の費用などに分解してみよう(9)
- 全てのデータが一度に与えられるコンペではユーザーの未来の行動の情報の特徴量を作ると強い ([TalkingData 1st](https://www.slideshare.net/TakanoriHayashi3/talkingdata-adtracking-fraud-detection-challenge-1st-place-solution))
    - 過去の行動よりも未来の行動に基づく特徴のほうがたいてい強い
        - 直前のクリックからの経過時間
        - 今後1時間以内でのそのカテゴリ変数の値の出現回数
- 過去のフラグ推移を連結した文字列をカテゴリ変数として扱う(7)
    - 水準数が100を超えるような場合に有効なことが多い
    - DeepLearningではなぜか効かないことが多い
    - 文字列ヒストグラムは[bosch 15th](https://www.slideshare.net/hskksk/kaggle-bosch)でも使われている
    - 時系列じゃなくても文字列連結してカテゴリ変数にするのは他のコンペでも使われている模様([kaggle: Porto Seguro's Safe Driver Prediction まとめ](https://copypaste-ds.hatenablog.com/entry/2019/02/08/170518))

**巨大データを扱う**

- データが大きいときは複数あるfoldのうち1つだけで検証するのもあり([Kaggle boschコンペ振り返り](https://www.rco.recruit.co.jp/career/engineer/blog/kaggle-bosch/))

**テクニック系**
うまくカテゴライズできなかったテクニック集


- [DenoisingAutoEncoder](https://github.com/GINK03/kaggle-dae)
- kNNによって観測された近傍点の数を特徴量として追加する(2: Facebook Predict check-insの1st place Winner’s solution)
- KMeans等のクラスタリングして、クラスタIDを使う or/and クラスタ中心までの距離を特徴量として加える([Malware '19 kernel](https://www.kaggle.com/aantonova/some-new-risk-and-clusters-features))
- 予測値を特徴量に入れてもう一度学習する(Home Credit)
- (Targetではなく)重要特徴量を予測するモデルを作って活用する ([HomeCredit 2nd](https://speakerdeck.com/hoxomaxwell/home-credit-default-risk-2nd-place-solutions?slide=13))
    - 予測した数値と実際の数値の差なども活用できる ([Elo 18th](https://www.kaggle.com/c/elo-merchant-category-recommendation/discussion/82107): 予測した日付と実際の日付の差)
    - 活用事例は(6)に詳しい
- メインデータとサブデータが1:多のような場合 (Home Credit や Elo の場合)
    - サブに時系列性があればカテゴリ変数は最後の値を取る、などが使える
    - サブデータにメインデータをマージしてサブだけで学習しすると効くことがある ([Home Credit 17th](https://www.kaggle.com/c/home-credit-default-risk/discussion/64503#378162) / [Elo 1st](https://www.kaggle.com/c/elo-merchant-category-recommendation/discussion/82036))
    - 先頭/末尾のN行のみでAggregationなども使える
        - Nの値をいろいろ変えるとなお良し
- 代表的な特徴量だけで計算したkNNで近傍500点のtargetの平均を特徴量として加える ([Home Credit 1st](https://www.kaggle.com/c/home-credit-default-risk/discussion/64821))
- Adversarial Validation できないように特徴量を加工してTrainへの過学習を避ける ([Malware '19 6th](https://www.kaggle.com/c/microsoft-malware-prediction/discussion/84069))
- [K近傍を用いた特徴量抽出](https://upura.hatenablog.com/entry/2018/06/23/165855)
- [FMを活用した特徴量作成](https://www.kaggle.com/c/talkingdata-adtracking-fraud-detection/discussion/56497)
- [Kaggle Past Solutions](https://ndres.me/kaggle-past-solutions/)
    - ちゃんと漁れば宝の山のはず
## **特徴量選択**
- Ridgeなどにかけて1変数ずつ有効かどうか確認するstepwiseが早い(5)
    - LightGBMなどでできたらそちらのほうが良いが、計算時間との兼ね合いで決めても良い。
- boruta, eli5, [NullImportances](https://www.kaggle.com/ogrellier/feature-selection-with-null-importances)
    - (今の所使って成功したことない…orz)
## **欠損値処理**

**見つけ方 & Fillana Approaches**

- 見つけ方: ヒストグラムをかこう
- Fill NA Approaches
    - カテゴリ変数か数値変数かによって欠損値の扱い方を変えよう
    - 適当なoutlierに書き換える
        - 木モデルの場合に有効
        - -99999や、MAX値+1とかmin値-1とか
    - 平均や中央値で書き換える
        - 線形モデルで有効
        - 木モデルにはネガティブ
    - カテゴリ変数の場合
        - 欠損値を「新たなカテゴリ」として扱うのも良い(3)
            - K個のカテゴリがあったら、欠損値もカテゴリとみなしたK+1個のOne Hot Encodingを行う
    - 推測する
- 注意
    - -99999とかで置き換えたときに CategorycalEncoding などに影響しないように注意
        - feature generationの前にfillnaしないほうが良い

**Tips**

- category_x_isnull の2値カテゴリーも有効
- Trainデータに出てこないカテゴリーがTestに出てきたら？
    - TrainとTestでの出現回数をカウントしてそれをカテゴリーにする
- XGBoost/LightGBMはNaNを直接扱える
- 値が0である説明変数の数を数えて追加の説明変数として加えてみる(2)
## **不均衡データの扱い**
- Over-/Under-sampling
    - SMOTEは効かないことが多い
    - Under-samplingでもきちんとバギングすれば精度出るから問題ない([TalkingData 1st](https://www.slideshare.net/TakanoriHayashi3/talkingdata-adtracking-fraud-detection-challenge-1st-place-solution))
    - [imbalanced-learn](https://imbalanced-learn.readthedocs.io/en/stable/#)の実装も使ってみよう
        - 利用例: [Porto Seguro Kernel](https://www.kaggle.com/rafjaa/resampling-strategies-for-imbalanced-datasets)
- 重みの変更
    - LightGBM: class_weight の調整とか
## **Validation**
- Good Validation is MORE IMPORTANT than Good Model.(8)
- validationのsplitはオーガナイザーのそれを模倣しよう
    - 模倣して良いValidationが出来たらTRUST LOCAL CV
    - 正しいValidationなしで進めるのは羅針盤無しで航海するのと同じ(進まないよ)
    - 「模倣しろ」の具体例: [Malware '19 6th](https://www.kaggle.com/c/microsoft-malware-prediction/discussion/84112)
        - ついでに、時系列的の場合のValidationの切り方の注意の勘所もわかって良い
- Normal-KFold か Stratified-Kfold か？
    - 多クラス分類ならStratified-Kfold必須(5)
    - 回帰でも1変数k-meansしてStratifiedにすることも(5)
- Adversarial Validation
    - CVとLBが一致しない場合trainとtestの分布が異なる恐れ
    - Testに近いTrainデータでValidationを行うのもあり
- LBを信じるべきか否か(8)
![](https://paper-attachments.dropbox.com/s_7E5490DED9B39C79CF7A296216B52BB82937D84344914574D87C4906555788A4_1602226209328_45f11db0-ae4c-4554-acaa-c3cd45dd8d63.png)



## **Ensemble / Stacking**
- [KAGGLE ENSEMBLING GUIDE](http://mlwave.com/kaggle-ensembling-guide/)が参考になる
- 序盤はEnsemble/Stackingによる安易なスコアアップは控えよう(2)
    - 時間と余裕があるうちに、他の多くの人はやらないような独創的な手法を模索しよう
    - 人と同じアプローチは後から取り入れれば良い
    - パラメータ変えたモデルを配合するだけでもスコア伸びるよ
- random seed average はもはや常識(4)
    - Averagingは単純だが強力な手法(2)
    - N乗平均、対数平均など試してみると良いかもしれない(2)
    - 小さなデータだったら平均アンサンブルで十分
- SingleModelの精度にこだわりすぎないように
    - Ensembleすることは大前提として多様な(相関の低い)モデル群、特徴量を作成しよう
    - 弱いモデルでもEnsembleで化けることがあるから念の為に取っておこう
        - [Ottoの例](https://www.slideshare.net/nishio/kaggle-otto): P57あたりから
    - Owen(8): The strongest individual model does not necessarily make the best blend.
    - とはいえ最近は A great model is better than ensemble of weak models という考え方も復活してきてはいる
        - CPMPさんが[Kaggle Days Paris](https://www.youtube.com/watch?v=fH_FiquKhiI&feature=youtu.be)で「bestfittingさんも言ってた」と紹介
        - CPMPさんはTalkingDataで48個の特徴量のLightGBMシングルモデルで大多数のチームのEnsembleモデルに勝ったとのこと
- GBMと相性が良いモデル
    - RandomForest(2)
    - Neural Network(理由: Tree Baseアルゴリズムは決定境界が特徴軸に平行な矩形になるが、NNなどは曲線(曲面)となるため(2))
    - Glmnet(RidgeやLASSO)(8)
- 浅いモデルと深いモデルを混ぜると効果あることがある
- Adversarial Validationが誤分類したデータを重視してEnsemble Weightをきめる(6)
- LightGBMとXGBoostをアンサンブルしてもあまり精度をあげられた例はない(7)
- Stakingの実装例: [Porto Seguro Kernel](https://www.kaggle.com/yekenot/simple-stacker-lb-0-284)
- AUCが評価指標のときはアンサンブルする前にrank化しよう([KAGGLE ENSEMBLING GUIDE](http://mlwave.com/kaggle-ensembling-guide/))
## **様々なモデル**
- Regularized Greedy Forest: くそ遅いけど試してみる価値あるよ
    - (相対的に)早い実装もある: [FastRGF](http://segafreder.hatenablog.com/entry/2018/01/08/214855)
- CatBoost: デフォパラメータが良いからめっちゃ時間の節約になるよ
- tSNE
    - 強力だけどパーフレシティのチューニングが大変難しい
    - TSNEのsklearnくそ遅いからtsneパッケージ推奨
## **学習のTips**
- 評価指標にとらわれなくても良い
    - 回帰問題でも target を[0, 1]に正規化し、xentropy lossを最適化するほうが精度が上がる場合がある([Avito 3rd](https://www.kaggle.com/c/avito-demand-prediction/discussion/59885) / [Avito 9th](https://www.slideshare.net/JinZhan/kaggle-avito-demand-prediction-challenge-9th-place-solution-124500050))
- (時系列データなどの場合) 特徴的な傾向があれば学習データ期間をずらしてしまうのもあり(7)
- XGBoostにおけるネタ(7)
    - 同じデータセットに対しては最適な学習回数はサンプル数に対しておよそ線形なので推測ができる(切片が0じゃないのでそこだけは注意)
    - 単一モデルでは学習回数が不足でも過剰でも精度が落ちるが、random seed averagingは学習回数過剰気味でもあまり精度が落ちない
    - max_depthが多いほどaveragingの効果が大きくなるので、単一モデル検証で精度が拮抗していたらより深い方を選ぶのがベター
    - 学習率は小さくすれば精度が良くなるが、averagingではその効果が大きく減少するので、必要以上に小さくしないほうが学習時間的に有利。
    - colsample_by* は小さくすると学習時間が線形に減少する。精度向上にも寄与することが多いので小さめの値を試してみよう。
## **Tuning**
- パラメータの意味は [この記事](https://nykergoto.hatenablog.jp/entry/2019/03/29/%E5%8B%BE%E9%85%8D%E3%83%96%E3%83%BC%E3%82%B9%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0%E3%81%A7%E5%A4%A7%E4%BA%8B%E3%81%AA%E3%83%91%E3%83%A9%E3%83%A1%E3%83%BC%E3%82%BF%E3%81%AE%E6%B0%97%E6%8C%81%E3%81%A1) がとてもわかり易いです
- XGBoost
    - max_depth=7あたりから始めるのがよい
    - Lightgbmは深さに加えて木全体の葉の数を制御できるので融通が利いて良い
    - bagging_fraction: １つの木を育てるのにどれくらいのデータを使って良いかを決める グリーン (オーバーフィッティングしたら下げればよい)
    - feature_fraction: １つの木を育てるのにどれくらいの特徴量を使って良いかを決める グリーン
    - min_data_in_leaf: 汎化に最も重要なパラメータ レッド (オーバーフィッティングしたら上げる)
    - lambda_l1, lambda_l2: これも重要、0,5,15,300 あたりを見てみる。
    - num_round: =何本の木を作るか。最初の方の木で十分に説明できてしまうこともある。
    - learning_rate: 高すぎると全く収束しない可能性ある、適切な小ささの学習率は汎化に有効。
    - trick: 以下を行うと大概精度が上がる (学習時間は長くなるが )
        - step * alpha
        - eta / alpha
    - boosting_type: dropoutを模したdartっていうのも有効
    - Owen流(8)
![](https://paper-attachments.dropbox.com/s_7E5490DED9B39C79CF7A296216B52BB82937D84344914574D87C4906555788A4_1602226243858_b64cf64b-b159-4fbc-bb10-a7316f4582b6.png)

    -
    -
    -
    - [CPMP流 (Kaggle Days Paris)](https://www.youtube.com/watch?v=fH_FiquKhiI&feature=youtu.be)
        - subsample=0.7 & 他はデフォから始めろ
        - min_child_weight: train/val gap 大きければ増やせ
        - それから max_Depth or number_of_leaves を調整しろ
        - LBがCVより低ければ正則化を強めろ
- LightGBM
    - なにはともあれ [公式 Parameters Tuning ガイド](https://lightgbm.readthedocs.io/en/latest/Parameters-Tuning.html)
    - num_leaves多め、feature_fractionかなり小さめ、とかもあり(5)
    - feature_fraction = sqrt(n_features)/n_features 程度だと特徴量数の影響を受けづらく、良い(5)
    - dartだとearly_stoppingしたときに挙動が変だから気をつけて
- Random Forest
    - gbmより深い木でも大丈夫なのでトライしてみよう
    - giniが大概良いが、たまにエントロピーが勝つ
- コンペの序盤と中〜終盤の2回やるという方法もある([Malware '19 Discussion](https://www.kaggle.com/c/microsoft-malware-prediction/discussion/82718#483205))
- Optunaが学習中断/再開とかもできて何かと便利
## **その他Tips**
- Testデータに対するsemi-supervised learningは現状で主要な差別化ポイントのひとつ(2)
- 終了直前で激強kernelが出現することがあるので対応が必要(5)
    - 特に銅メダル圏内ぐらいのとき
    - 最終日は2サブ残しておき自分のsolutionとアンサンブルできるようにしておけば安心
    - 1サブ残しだと精神的にきついので、2サブ推奨らしい
## **黒魔術系**

まだ使ってないけど役に立ちそうなもの


- [Linear Quiz Blending](https://speakerdeck.com/smly/techniques-tricks-for-data-mining-competitions?slide=24)
- 遺伝的プログラミングによる特徴量作成
    - 活用例: [HomeCredit 2nd](https://speakerdeck.com/hoxomaxwell/home-credit-default-risk-2nd-place-solutions?slide=21)
    - [Porto Seguro Kernel](https://www.kaggle.com/scirpus/big-gp)
## **未整理: 使えるライブラリとか**

あとでまとめる(かも)


- [pandas-profiling](https://qiita.com/h_kobayashi1125/items/02039e57a656abe8c48f)
- [コピペで使える。Kaggleでの実験を効率化する小技まとめ](https://amalog.hateblo.jp/entry/kaggle-snippets)
- [あなたの生産性を向上させるJupyter notebook Tips](https://recruit-tech.co.jp/blog/2018/10/16/jupyter_notebook_tips/)
- [Jupyter Notebook Viewer](https://github.com/tuxu/nbviewer-app)
- [kaggle-apiというKaggle公式のapiの使い方をまとめます](http://www.currypurin.com/entry/2018/kaggle-api)
- [JupyterLabのおすすめ拡張機能7選](https://qiita.com/canonrock16/items/d166c93087a4aafd2db4)
- [Vaex入門 / 可視化もXGBoostも](https://blog.ikedaosushi.com/entry/2019/04/14/173307)
- [Preemptive Instance のシャットダウン時に使えるScript](https://cloud.google.com/compute/docs/instances/create-start-preemptible-instance#handle_preemption)
- [pandas.DataFrame のforループをゆるふわ△改良して300倍高速化する](https://kunai-lab.hatenablog.jp/entry/2018/04/08/134924)

[決定木は本当に変換に依存しないのか？ - 天色グラフィティ](https://amalog.hateblo.jp/entry/decision-tree-scaling)
決定木をベースとした手法はStandardScalerなどの線形な変換に対しては依存しないことが分かりました。
RandomForestでは、分割境界が特徴量の中点に設定されることから、log1pなどの非線形変換については結果が微妙に変わることがあるようです。
LightGBMでは内部でbinningが行われている関係上、RandomForest以上に変換に対して頑健になることが分かりました。

[log1p変換？ どうして？ | カグル](https://www.kaggle.com/c/favorita-grocery-sales-forecasting/discussion/47124)
numpy.log1p
log1pを使用して販売台数を変換すると、評価メトリックは重み付きrmseと同じになり、お気に入りのアルゴリズムの組み込みの目的関数（損失関数）を使用できます。
次の質問は、なぜ彼らがlog1pを選択したのですか？2つの理由：

1. ログ変換により、大規模な売上は小規模な売上と同じように見えます。
   ベースラインが何であれ、a％の変動は同じコストになります。

2. Ceshine Leeが書いたように、販売単位が0になる可能性があるため、ログ変換を直接適用することはできません。
    Log1pはこれを軽減する方法の1つですが、変動コストの優れた特性をベースラインから独立させることはできません。
     私は両方を最大限に活用するために、aが1よりも大幅に大きいlog（1 + ax）のようなものを使用していました。
     これがWebトラフィック予測のコンテストで使用したもので、log1pよりもはるかに優れた結果をもたらしました。
     しかし、とにかくlog1pを使用して評価メトリックを一致させる必要があります…
