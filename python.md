# python


# 未分類

デコレータの説明
[【Python入門】デコレータの使い方をわかりやすく解説！ | 侍エンジニア塾ブログ（Samurai Blog） - プログラミング入門者向けサイト](https://www.sejuku.net/blog/25130#:~:text=%E3%83%87%E3%82%B3%E3%83%AC%E3%83%BC%E3%82%BF%E3%81%A8%E3%81%AF%E3%80%81%E3%81%99%E3%81%A7%E3%81%AB%E3%81%82%E3%82%8B,%E5%87%BA%E6%9D%A5%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%AA%E3%82%8A%E3%81%BE%E3%81%99%E3%80%82)
[Python小ネタ: withとcontextmanagerで実行時間を計測する - Qiita](https://qiita.com/kazuhirokomoda/items/1e9b7ebcacf264b2d814)


listのアスタリスク
[Python のアスタリスク (*) による List のアンパック - Python の基本 - Python 入門](https://python.keicode.com/lang/list-unpack-asterisk.php)


## **複数の変数に同じ値を代入**

`=`を連続して使うと複数の変数に同じ値を代入できる。複数の変数を同じ値に初期化する場合などに便利。

a = b = 100
# シンプルなダウンロード
```py
import urllib.request
import sys

def download(url, title):

    urllib.request.urlretrieve(url,"{0}".format(title))

url = 'https://raw.githubusercontent.com/jacobstallone/Time_Series_ARIMA--Blog-and-code-/master/AirPassengers.csv'
title = 'AirPassengers.csv'
download(url, title)
```

# jyupyter
```py
!mkdir data
!wget https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_multilingual_JP_v1_00.tsv.gz -P data/
!gunzip -d data/amazon_reviews_multilingual_JP_v1_00.tsv.gz
```


# pip
```py
# --yes
pip uninstall --yes torch torchvision
```


# read write
```py
# ファイル読込、一行ずつリスト化
with open(path, encoding='utf8') as f:
    l_strip = [s.strip() for s in f.readlines()]



# 一行ずつ書き込み
with open(path_w, mode='w', encoding='utf8') as f:
    for row in result:
        f.write(row + '\n')


# リストをタブ区切りで書き込む
with open(path_w, mode='a', encoding='utf8') as f:
    f.writelines('\t'.join(result) + '\n')
```

書込み

```py
# generatorを一行でファイルに書き込み
import csv
with open('test.txt', 'w') as f:
    writes = csv.writer(f, delimiter=',', quoting=csv.QUOTE_ALL)
    writes.writerows(gen(reader)))
```


# sort

# リストのメソッド（戻り値なし）
```py
files.sort(reverse=True) # True:降順
```

# 組み込み
```py
sorted(files, reverse=True)
```

# タプルのソート（一度リストになるのでタプルに戻す）
```py
org_tuple = (3, 1, 4, 5, 2)
new_tuple_list = sorted(org_tuple)
new_tuple = tuple(new_tuple_list)
```



# pickle & joblib.dump

どちらも同じ感じ
joblib.dump の方がpickleより遅いが、圧縮率は大きい


https://takaishikawa42.hatenablog.com/entry/2019/01/17/225615





# pep8

Python命名規則一覧

| **対象** | **ルール**               | **例**                |
| ------ | --------------------- | -------------------- |
| パッケージ  | 全小文字 なるべく短くアンダースコア非推奨 | tqdm, requests ...   |
| モジュール  | 全小文字 なるべく短くアンダースコア可   | sys, os,...          |
| クラス    | 最初大文字 + 大文字区切り        | MyFavoriteClass      |
| 例外     | 最初大文字 + 大文字区切り        | MyFuckingError       |
| 型変数    | 最初大文字 + 大文字区切り        | MyFavoriteType       |
| メソッド   | 全小文字 + アンダースコア区切り     | my_favorite_method   |
| 関数     | 全小文字 + アンダースコア区切り     | my_favorite_funcion  |
| 変数     | 全小文字 + アンダースコア区切り     | my_favorite_instance |
| 定数     | **全大文字** + アンダースコア区切り | MY_FAVORITE_CONST    |

# 置換

正規表現の置換はreplace()ではなくre.sub()
```py
import re #最初にreモジュールをインポート。
re.sub(正規表現パターン（=置換したい文字）, 新しい文字, 対象の文字列 [,置換回数])
```



# リスト

リスト中の数字⇔文字列 変換
```py
user_list = [1, '2', 3]
print(list(map(str, user_list)))
print(list(map(int, user_list)))
```

リスト内の空要素を削除
```py
l = ['A', 'B', '', '', '', '', 'C', '', '', '', '', '', '', '', '', '', '', '', '', '']
l2 = [x for x in l if x]
l2 ['A', 'B', 'C']
```

リスト内要素削除
```py
l = ['Alice', 'Bob', 'Charlie', 'Bob', 'Dave']
l.remove('Alice')
```


## sort
```py
org_list = [3, 1, 4, 5, 2]

org_list.sort()
print(org_list)
```


# 条件判定

リスト内に含まれるか
```py
if '人' in raw_list:
```

偶数奇数判定

```py
if num % 2 == 0:
     print('偶数')
else:
     print('奇数')
```

特定の文字列から始まる場合
startswithの場合、タプルしか受け付けない

```py
DEL_RAW_WORDS_START = ('Ａ口座', 'Ｂ口座', '銀行名称', '振込額計', '所属コード', '税扶養数')
if not raw.startswith(DEL_RAW_WORDS_START):
```


# 辞書

単純に辞書を作る

```py
number_dict = {"1": "first","2": "second","3": "third",}
```

リストから辞書を生成

```py
sample_list = [[1, 2], [3, 4], [5, 6]]
dict(sample_list)
```


辞書同士の連結（結合）

```py
d1 = {'k1': 1, 'k2': 2}
d2 = {'k1': 100, 'k3': 3, 'k4': 4}

d1.update(d2)
```

# 文字列分割

複数の区切り文字を考慮したい場合、re.splitで正規表現で分割する
後ろから分割したい場合はrsplit

```py
import re
if i > 0:
    if raw.startswith('20H超所定外'):
        raw_list = re.findall('20H超所定外|\d+\.\d+', raw)
    else:
        raw_list = re.split('(\d+\.\d+|\d+,\d+|\d+)', raw)
```



参考
[Pythonを書き始める前に見るべきTips - Qiita](https://qiita.com/icoxfog417/items/e8f97a6acad07903b5b0#)

クラスとは「何らかのデータ（インスタンス変数）と、それらを処理するためのコード（メソッド）をひとまとめにして名前を付けることで、後からそれらを再利用する」ための仕組み
なお、モジュールやパッケージでクラスを定義すれば、それらももちろんインポートして利用できるようになる


# 可変長引数

Pythonでは仮引数の変数名に*や**を用いることで任意個数の引数をタプルまたは辞書型で渡すことが可能
  *args : 任意個数の引数をタプルとして受け取る
  **kwargs : 任意個数の引数を辞書として受け取る
*argsの例

```py
def hoge1(*args):
    print(args)
    print(type(args))

hoge1(1,2,3,4,5)
# 実行結果
# (1, 2, 3, 4, 5)
# <class 'tuple'>
```


# classまとめ

class まとめ
サブクラス（LGBなど）をインスタンス化する際、
スーパークラス（BaseModel）を継承し、スーパークラスの共通部分を使うようにし、
サブクラスごとに独自部分はサブクラス内で定義されたメソッドにオーバーライド（上書き）される

処理の流れとしては、まずサブクラスを引数付きで呼び出すと、
コンストラクタ「def __init_(self, arg1, arg2…)」が実行され、
その中に関数があれば実行される

実行後、コンストラクタのtrainsetやypred、modelなどに後からアクセスが可能


コンストラクタの意味

classの__init__

変数の初期化

クラス内の関数実行後の返り値を保持しておける

→予測結果とか

インスタンス化した後に属性として呼び出せる

継承するには同じ関数をスーパークラス、サブクラスともに定義する必要がある

→オーバーライド（スーパークラスの属性やメソッドを利用できる）




```py
class test:
  pass


calass test(object):
  pass

()内は基底クラス（基となるクラスを指定する。defaultはobjectクラス）

# 現在のローカルスコープに存在する名前を要素とするリストを返す
dir(test)
```

__class__ などはobjectクラスのメソッドである

__class__ などはobjectクラスのメソッドである

super().__init__() は Prism クラスの __init__ メソッドを実行しているだけです。
__init__() メソッドは親クラスの Prism でも、子クラスでの Cube でも定義されています。このような場合、子クラスのものが勝ち、上書きされます。これをオーバーライドと言います。でも、親クラスのメソッドや変数を呼んだり参照したいケースもでてきます。そのときに使うのが super() です。
今回のコードでもそうですが、super().__init__() で親クラスのコンストラクタを実行しつつ、追加処理を行うというチョイ足しのときに使う場合が多いです。



# 三項演算子、リスト内表記、ラムダ式

三項演算子

```py
result = 'even' if 1 == 0 else 'odd'
result = a * 2 if 1 == 0 else a * 3
result = 'even' if 1 == 0 and 1 == 1 else 'odd'
```

括弧で囲えば改行を入れられる

```py
x = (10 if a > 0 else
    20 if a == 0 else
    30)
```


## リスト内表記 ※iterableを処理する手法

```py
[式 for 任意の変数名 in イテラブルオブジェクト]
squares = [i**2 for i in range(5)]
[式 for 任意の変数名 in イテラブルオブジェクト if 条件式]
odds = [i for i in range(10) if i % 2 == 1]
```


## ラムダ式（無名関数） ※pep8的にはlamda式には名前を付けない（つける場合はdef 関数にする）
```py
get_odd_even = lambda x: 'even' if x % 2 == 0 else 'odd'
print(get_odd_even(1))
```


## リスト内包表記応用
```py
# zip()で二つのiterableを同時に処理
l_zip = [(s1, s2) for s1, s2 in zip(l_str1, l_str2)]
l_sub = [i2 - i1 for i1, i2 in zip(l_int1, l_int2)]

# 三項演算子 の組合せ
l_zip_if = [(s1, s2) for s1, s2 in zip(l_str1, l_str2) if s1 != 'b']

# enumerate()でindexとともに処理
l_enu = [(i, s) for i, s in enumerate(l_str1)]

# iterable要素を条件抽出（リストの条件抽出） for -> if -> 先頭の式
odds = [i for i in range(10) if i % 2 == 1]

# [条件式が真のときに評価される式 if 条件式 else 条件式が偽のときに評価される式]
l = ['even' if i % 2 == 0 else i for i in range(10)]
l = [i * 10 if i % 2 == 0 else i for i in range(10)]

# 二つのiterable
df_list = [pd.DataFrame([[1] * len(val)], columns=val, index=[index])
                  if isinstance(val, list) else pd.DataFrame(index=[index])
                  for val, index in zip(x.values, x.index)]
```


## 集合内包表記（set型オブジェクト）

※set型は重複しない要素（同じ値ではない要素、ユニークな要素）のコレクション

```py
{式 for 任意の変数名 in イテラブルオブジェクト}
s = {i**2 for i in range(5)}
辞書内包表記
{キー: 値 for 任意の変数名 in イテラブルオブジェクト}
l = ['Alice', 'Bob', 'Charlie']
d = {s: len(s) for s in l}
```

# key & valueから辞書を生成

```py
keys = ['k1', 'k2', 'k3']
values = [1, 2, 3]
d = {k: v for k, v in zip(keys, values)}
```


## ジェネレータ式（ジェネレータを返す）

 ※ メモリの使用量を抑えたい場合など

```py
g = (i**2 for i in range(5))
for i in g:
    print(i)
```


## ジェネレータ式とif
```py
g_cells = ((row, col) for row in range(0, 3)
          for col in range(0, 2) if col == row)
for i in g_cells:
    print(i)
```


## タプル内包表記
```py
tuple(i**2 for i in range(5))
```

# ファイル解凍

```py
import zipfile
```

## ファイルをZIPファイルに圧縮(複数も可能)
```py
with zipfile.ZipFile('data/temp/new_comp.zip', 'w', compression=zipfile.ZIP_DEFLATED) as new_zip:
    new_zip.write('data/temp/test1.txt', arcname='test1.txt') # arg1 対象ファイル、 arg2 arg1ファイルをarg2のファイル名でzipに書き出す
    new_zip.write('data/temp/test2.txt', arcname='zipdir/test2.txt')
    new_zip.write('data/temp/test3.txt', arcname='zipdir/sub_dir/test3.txt')


import zipfile

path = r'F:\home\BOOK\!200_趣味_漫画\'
extract_dir = r'C:\tmp'

# path配下のの圧縮ファイルを全て解凍する
for zip_file in os.listdir(path):
    zip_path = os.path.join(path, zip_file)
    if os.path.isfile(zip_path):
        print(zip_path)
        # zip_path
        with zipfile.ZipFile(zip_path) as z:
            z.extractall(extract_dir)
```


## 既存のZIPファイルに新たなファイルを追加
```py
with zipfile.ZipFile('data/temp/new_comp.zip', 'a') as existing_zip:
    existing_zip.write('data/temp/test4.txt', arcname='test4.txt')
```


## ディレクトリ（フォルダ）をZIPファイルに圧縮
```py
import shutil
shutil.make_archive('data/temp/new_shutil', 'zip', root_dir='data/temp/dir') # arg1 圧縮先ファイル名（拡張子なし）
```


## ZIPファイルの中身を確認
```py
with zipfile.ZipFile('data/temp/new_comp.zip') as existing_zip:
    print(existing_zip.namelist())
```


## ZIPファイルの中身をすべて解凍（展開）
```py
with zipfile.ZipFile('data/temp/new_comp.zip') as existing_zip:
    existing_zip.extractall('data/temp/ext')
```


## zip内に日本語があり文字化けする
```py
with zipfile.ZipFile('data/temp/new_comp.zip') as z_info:
    for z_info in z.infolist():
        z_info.filename = z_info.filename.encode('cp437').decode('sjis')
        z_info.extract(z_info, dir_path)
```

## ZIPファイルの中身を選択して解凍（展開）
```py
with zipfile.ZipFile('data/temp/new_comp.zip') as existing_zip:
  existing_zip.extract('test1.txt', 'data/temp/ext2') # arg1: 対象圧縮ファイル arg2: 解凍先DIr
```


## .gzへ圧縮
```py
import gzip
import shutil
with open(output_file, 'rb') as f_in:
with gzip.open(output_file + '.gz', 'wb') as f_out:
shutil.copyfileobj(f_in, f_out)
```


## windos系ファイルの解凍
```py
!apt-get install unrar
!apt-get install p7zip-full

!7z x -o'data/bearing_data' 'Bearing_Sensor_Data.7z'
!unrar x '/content/data/bearing_data/2nd_test.rar'
```


## rar解凍

[python — pythonでファイルを解凍する](https://www.it-swarm-ja.tech/ja/python/python%E3%81%A7%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%92%E8%A7%A3%E5%87%8D%E3%81%99%E3%82%8B/969149575/)
[Pythonでrarファイルを解凍する - Information High](https://ky-orihara.hatenablog.com/entry/2019/02/19/022738)

```py
# 事前インストール
# pip install rarfile
# pip install unrar
import rarfile

inputpath = r"F:\home\BOOK\!900_その他分類\@200_趣味_ファッション\いまさら聞けない洋服のマナー\いまさら聞けない洋服のマナー.rar"

rf = rarfile.RarFile(inputpath)

# rarファイルを展開する
rf.extractall()

ファイル情報を取得する場合
for f in rf.infolist():

    # rarファイルの内容を出力
    print(f.filename)

    # 1ファイルずつ展開
    # ただし、ディレクトリを展開すると、フォルダ構成がおかしくなるので、必要に応じて展開をスキップするようにすること。
    rf.extract(f.filename)
```


# ダウンロード

```py
import requests
# from tqdm import tqdm
from tqdm import tqdm_notebook as tqdm # ipython_notebookの場合こちら

file_url = 'https://academicgraphv2.blob.core.windows.net/oag/mag/paper/mag_papers_0.zip'
file_size = int(requests.head(file_url).headers["content-length"])

res = requests.get(file_url, stream=True)
pbar = tqdm(total=file_size, unit="B", unit_scale=True)

with open('mag_papers_0.zip', 'wb') as file:
    for chunk in res.iter_content(chunk_size=1024):
        file.write(chunk)
        pbar.update(len(chunk))
    pbar.close()
```

# 辞書の並び替え
```py
sorted(score.items(), key=lambda x:x[0])

sorted(score.items(), key=lambda x:x[1])

sorted(scores, key=lambda x:x['kokugo'])
```

# DASK


https://qiita.com/simonritchie/items/e174f243bc03fb25462e

https://qiita.com/R1ck29/items/9b75b630b37c9e3e4b3f

# ファイル名取得
```py
import glob
glob.glob("/test_dir/dir_A/*")

# Windowsでは'\\'でもディレクトリを区切れます
glob.glob("\\test_dir\\dir_A\\*")

# 相対パスでも指定可能です
import os
os.chdir("/test_dir/dir_A")
glob.glob("./*")

# 再帰的な検索
# python 3.5以降
glob.glob("/test_dir/**.txt", recursive=True)
```


## 最新のファイルを取得
```py
import glob
import os
list_of_files = glob.glob('/path/to/folder/*') # * means all if need specific format then *.csv
latest_file = max(list_of_files, key=os.path.getctime)
```



## watermark
```py
!pip install watermark > /dev/null
%load_ext watermark
%watermark -a 'Sebastian Raschka' -u -d -p numpy, pandas, matplotlib, sklearn
```


## フルパスからファイル名を取得: os.path.basename()
 ```py
   # 拡張子ありのファイル名
basename = os.path.basename(filepath)

# 拡張子なしのファイル名
basename_without_ext = os.path.splitext(os.path.basename(filepath))[0]
```



## フルパスからディレクトリ名を取得
```py
dir_path = os.path.dirname(inputpath)
```

# ディレクトリ（フォルダ）を作成: os.mkdir()
```py
import os

new_dir_path = 'data/temp/new-dir'

os.mkdir(new_dir_path)
```


# ファイルディレクトリ存在確認: os.path.exists()
```py
# ファイルでもディレクトリでも、存在していればTrue、存在していなければFalseを返す。
os.path.exists(filepath)
```

# ファイルを移動
```py
import shutil
# 第一引数: 移動させたいファイルやディレクトリのパス
# 第二引数: 移動先のディレクトリのパス
# shutil.move()は移動後のファイルやディレクトリのパスを返す
new_path = shutil.move('temp/dir1/file.txt', 'temp/dir2/')
```



# 環境変数
```py
# ホームディレクトリの取得
import os
from os.path import expanduser

print os.environ['HOME']
# win対応
print(expanduser("~"))
```



## ホームを起点にchdir
```py
os.chdir(os.path.join(expanduser("~"), r'@my_folder\tmp'))
```

# メモリ

メモリを確保している変数

```py
%whos
```



# パラメータ展開？
```py
aaa = 'abc%dxxx'
[aaa % i for i in range(0,3)]
['abc0xxx', 'abc1xxx', 'abc2xxx']
```



# その他

二つの日付から等間隔な日付のリストを出力

```py
from datetime import datetime
from datetime import timedelta
def daterange(_start, _end, interval=5, reverse=False):
    _start = datetime.strptime(_start, '%Y-%m-%d')
    _end  = datetime.strptime(_end, '%Y-%m-%d')
    for n in range(abs((_end - _start).days)):
        if reverse:
            n =+ n*-interval
            if _start + timedelta(n) > _end:
                yield _start + timedelta(n)
        else:
            n =+ n*interval
            if _start + timedelta(n) < _end:
                yield _start + timedelta(n)
# days = [i.strftime("%Y-%m-%d") for i in daterange('2020-03-10', '2020-03-31', interval=30, reverse=False)]
```

↑pandasで出来る

```py
# 後ろへ30日ずつ5回分
days1 = pd.date_range(end='2020-02-18', freq='30D', periods=5).to_list()
# 前へ30日ずつ5回分
days2 = pd.date_range(start='2020-02-18', freq='30D', periods=5).to_list()
days = days1 + days2
days.sort()
```


## 変換（置換）テーブルを作成
https://qiita.com/tag1216/items/df6c93bdb823dd48af6c

```py
table = str.maketrans('、。', ',.', '・')
result = text.translate(table)




# 全角半角置換
# Unicode表に従い、「!」(0x0021)から94文字を半角データとして使用
# Unicode表に従い、「！」(0xFF01)から94文字を半角データとして使用
ZEN = "".join(chr(0xff01 + i) for i in range(94))
HAN = "".join(chr(0x21 + i) for i in range(94))


# 変換テーブルを作成する（maketrans(変換前、返還後、削除)）
ZEN2HAN = str.maketrans(ZEN, HAN)
HAN2ZEN = str.maketrans(HAN, ZEN)


text = 'Ａ口座263,263円Ｂ口座0円'
result = text.translate(ZEN2HAN)
print(result)
```


# 日付

指定した間隔の日付のリストを生成する

```py
from datetime import datetime
from datetime import timedelta

def daterange(_start, _end, interval=5, reverse=False):
    _start = datetime.strptime(_start, '%Y-%m-%d')
    _end  = datetime.strptime(_end, '%Y-%m-%d')
    for n in range(abs((_end - _start).days)):
        if reverse:
            n =+ n*-interval
            if _start + timedelta(n) > _end:
                yield _start + timedelta(n)
        else:
            n =+ n*interval
            if _start + timedelta(n) < _end:
                yield _start + timedelta(n)
# days = [i.strftime("%Y-%m-%d") for i in daterange('2020-03-10', '2020-03-31', interval=30, reverse=False)]
days1 = [i.strftime("%Y-%m-%d") for i in daterange('2020-02-18', '2019-10-01', interval=30, reverse=True)]
days2 = [i.strftime("%Y-%m-%d") for i in daterange('2020-02-18', '2020-03-31', interval=30, reverse=False)]
days = days1 + days2
print(days)
```

pandas関数がありそう。。。

```py
pd.date_range('2018-01-01', '2018-01-31', freq='2D'))
```


## 日付のリストを2点間から作成
```py
# Python3で開始日と終了日からその間の日付リスト（あるいは時間リスト）を得る
datelist = map(lambda x, y=strdt: y + timedelta(days=x), range(days_num))
datelist = [strdt + timedelta(days=x) for x in range(days_num)]
```


その他
処理時間計測
# 処理時間、メモリ、件数の比較

# 処理時間、メモリ、件数の比較
 ```py
   import time
for n in [1000, 10000, 100000]:
    _df = df.sample(n=n, random_state=0)
    t1 = time.time()
    df_final, df_final_sparse = get_pre_df(df.sample(n=n, random_state=0))
    t2 = time.time()
    mem = df_final.memory_usage(deep=True).sum() / 1024**2
    print(f'{n}\t{mem}\t{t2-t1}')
```

論理演算

- 和集合（合併、ユニオン）: |演算子, union()
- 積集合（共通部分、交差、インターセクション）: &演算子, intersection()
- 差集合: -演算子, difference()
- 対称差集合: ^演算子, symmetric_difference()
- 部分集合か判定: <=演算子, issubset()
- 上位集合か判定: >=演算子, issuperset()
- 互いに素か判定: isdisjoint()
# リスト同士の比較
# setは順番は保証されない
orted(list(set(train_cols) ^ set(cols)))

メモリの確認

```py
import psutil
print(psutil.virtual_memory().available /1024**3)
print(psutil.virtual_memory().used /1024**3)
print(psutil.virtual_memory().active /1024**3)
print(psutil.virtual_memory().inactive /1024**3)
print(psutil.virtual_memory().buffers /1024**3)
print(psutil.virtual_memory().cached /1024**3)
print(psutil.virtual_memory().shared /1024**3)
print(psutil.virtual_memory().slab /1024**3)
```



# pdf操作
- pdf2image と Poppler と 日本語化ファイルが必要（pipインストール可能なのはpad2imageだけで）
- 参考URL
- [リファレンス—pdf2image最新のドキュメント](https://pdf2image.readthedocs.io/en/latest/reference.html?highlight=save#convert-from-path-convert-from-bytes)
- [PythonでPDFを画像ファイル（JPEG、PNG）に変換する方法 | ガンマソフト株式会社](https://gammasoft.jp/blog/convert-pdf-to-image-by-python/#pdf2image)
- [PythonでPDFをまとめて画像に変換する - Qiita](https://qiita.com/kikuyan8540/items/35751c573de014df205b)
- [Poppler : PDFのコマンドラインツール | PDF](http://pdf-file.nnn2.com/?p=863)
- `pip install pdf2image`
```py
import os
from pathlib import Path
from pdf2image import convert_from_path

def get_cover_from_pdf(inputpath, outputpath):

    # poppler/binを環境変数PATHに追加する
    os.environ["PATH"] += os.pathsep + r'C:\Users\itino\@my_folder\poppler\bin'

    # PDFファイルのパス
    pdf_path = Path(inputpath)

    # PDF -> Image に変換（150dpi）
    pages = convert_from_path(str(pdf_path), 150, last_page=1)

    # 画像ファイルを１ページずつ保存
    for i, page in enumerate(pages):
        jpg_out_path = os.path.join(outputpath, pdf_path.stem + ".jpg")
        print(jpg_out_path)
        # JPEGで保存
        page.save(jpg_out_path, "JPEG")

inputpath = r"F:\home\BOOK\test\300万人に支持された オレンジページnetのBESTおかず.pdf"
outputpath = r"F:\home\BOOK\test"
get_cover_from_pdf(inputpath, outputpath)
```


# epub
## epubのcover画像を取得し保存する
- [python でバイナリデータから Image オブジェクトを生成する方法 - dak ブログ](https://blog.goo.ne.jp/dak-ikd/e/b083c3e49acff0b5555b78aa8dc99587)
- [ebooklib Package — EbookLib 0.17 documentation](http://docs.sourcefabric.org/projects/ebooklib/en/latest/ebooklib.html)
- `pip install ebooklib`

```py
import ebooklib
from ebooklib import epub
from pathlib import Path

from PIL import Image
import io

def get_cover_from_epub(inputpath):

    # ファイルのパス
    epub_path = Path(inputpath)
    jpg_out_path = os.path.join(os.path.dirname(inputpath), epub_path.stem + ".jpg")

    if not os.path.exists(jpg_out_path):
        # epub読込
        book = epub.read_epub(epub_path)
        # cover部分読込
        image = book.get_item_with_id('cover')
        # コンテンツ取得（バイナリデータ）
        img_bytes = image.get_content()

        # バイナリ to Image
        inst = io.BytesIO(img_bytes)
        img = Image.open(inst)
        img.save(jpg_out_path)
        print(f'[maked jpg] : {jpg_out_path}')
    else:
        print(f'[exists jpg] : {jpg_out_path}')

inputpath = r"F:\home\BOOK\!317_仕事_機械学習\kaggleで上位に入るための探索的データ解析入門\kaggleで上位に入るための探索的データ解析入門.epub"
get_cover_from_epub(inputpath)
```



# zipfile 先頭ファイルのみ取り出す
```py
import zipfile

def get_cover_from_zip(inputpath):

    # ファイルのパス
    zip_path = Path(inputpath)
    dir_path = os.path.dirname(inputpath)
    jpg_out_path = os.path.join(dir_path, zip_path.stem + ".jpg")

    if not os.path.exists(jpg_out_path):
        # zip 先頭ファイルダウンロード
        with zipfile.ZipFile(inputpath) as z:
            zip_info_first = z.infolist()[0]
            zip_info_first.filename = zip_info_first.filename.encode('cp437').decode('sjis')
    #         print(zip_info_first.filename)
            z.extract(zip_info_first, dir_path)
            # リネーム
            os.rename(os.path.join(dir_path, zip_info_first.filename), jpg_out_path)
        print(f'[maked jpg] : {jpg_out_path}')
    else:
        print(f'[exists jpg] : {jpg_out_path}')

inputpath = r"F:\home\BOOK\!900_その他分類\@200_趣味_ファッション\別冊ライトニングVol.67 男の手仕事 メンテナンス・マニュアル\別冊ライトニングVol.67 男の手仕事 メンテナンス・マニュアル.zip"
get_cover_from_zip(inputpath)
```


# rarから先頭ファイルのみ取り出す
```py
# 事前インストール
# pip install rarfile
# pip install unrar
from rarfile import RarFile
import os
from os.path import expanduser
import shutil

def get_cover_from_rar(inputpath):

    rar_path = Path(inputpath)
    jpg_out_path = os.path.join(os.path.dirname(inputpath), rar_path.stem + ".jpg")

    if not os.path.exists(jpg_out_path):
        # 先頭ファイルのみ解凍する
        os.chdir(os.path.join(expanduser("~"), r'@my_folder\tmp'))
        with RarFile(inputpath) as rf:
            extract_path = rf.infolist()[0].filename
            rf.extract(extract_path)
        # 解凍ファイルを移動
        shutil.move(extract_path, jpg_out_path)
        # 解凍時のフォルダを削除
        shutil.rmtree(os.path.dirname(extract_path))
        print(f'[maked jpg] : {jpg_out_path}')
    else:
        print(f'[exists jpg] : {jpg_out_path}')
    # ホームへ戻る
    os.chdir(expanduser("~"))

inputpath = r"F:\home\BOOK\!900_その他分類\@200_趣味_ファッション\いまさら聞けない洋服のマナー\いまさら聞けない洋服のマナー.rar"
get_cover_from_rar(inputpath)
```
