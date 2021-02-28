```sh
#-------------------------------------------------------------------
## スタイルガイド
#  参考サイト：https://qiita.com/laqiiz/items/5f72ca668f1c58176644

# 利用用途：小さいツールやスクリプト実行時のラップ程度にShellの利用（100行超えるとPythonとか他の言語を使う）
# インデント:  2スペース
# 変数名 :     定数、環境変数（大文字のSNAKE_CASE）
#             それ以外（小文字のsnake_case）
# ファイル名 :  lower_case.shの形式
# 関数名    :  lower_caseで命名
#-------------------------------------------------------------------
#!/bin/bash           # Bashのみ利用可能とする
#
# データベースのバックアップ用途
export PATH='/usr/xpg4/bin:/usr/bin:/opt/csw/bin:/opt/goog/bin'
#######################################
# 関数：機能の内容
# Globals:
#   BACKUP_DIR
#   ORACLE_SID
# Arguments: 引数
#   None
# Returns: 戻り値
#   None
#######################################
err() {
  local message="$1"                                    # ローカル変数 関数内でしか用いない変数にはlocalで宣言する(グローバルネーム空間の汚染を防ぐ)
  echo "[$(date +'%Y-%m-%dT%H:%M:%S%z')]: $@" >&2       # エラーメッセージはSTDERRに出力（正常な状態と異常な状態を簡単に区別できるようにする）
}

if ! do_something; then
  err 'Unable to do_something'
  echo 'finish'                                         # 文字列の場合はシングルクォーテーション
  exit "${E_DID_NOTHING}"                               # 変数展開の場合はダブルクォーテーション
fi

# パイプ : 複数なら改行
#          前後にスペース
cmd1 | cmd 2
cmd1 \
  | cmd2 \
  | cmd3

# 変数展開  ${}で囲う
#   例外 "引数インデックス: $1" "$5" "$3" や "特殊系: !=$!, -=$-, _=$_. ?=$?, #=$# *=$* @=$@ \$=$$ ..."）
# ループ 「;とdoとthenは同じ行に書く(forとwhileとif)」
for file in ${files}; do                    # ループ変数をsとかzとか使わない（インデックスのiやjはOK）
  if [[ "filename" =~ ^[[:alnum:]]+]]; then # [[ ... ]] は引数なしや複数引数の場合でもエラーにならず、正規表現が使えるため推奨
    var2=&(echo abc)                        # バッククォートではなく$(command)が推奨
  fi
done
#-------------------------------------------------------------------

#-------------------------------------------------------------------
## ハイフンで標準入出力
Example
~$ cat -
hoge (←入力)
hoge

# 置換後(sed等で)のファイル（想定）と元のファイルを比較したい時など
sed "s/abc/def/g" test1.txt | diff - test2.txt

# 出力結果をハイライト付きで見たい
diff test1.txt test2.txt | vim -


# 環境変数、シェル変数について
#   シェル変数の場合、現在のシェルプロセス内でのみ有効
#   環境変数の場合、子シェルプロセスに引き継がれる
a=1; echo $a    # return 1
sh -c 'echo $a' # return null
export a=1; sh -c 'echo $a' # return 1

#-------------------------------------------------------------------
# 環境変数
#   シェル変数: 現在実行中のシェルだけで有効
#   環境変数: シェルから実行したコマンドにも引き継がれる
#     exitすれば初期化される
#   永続的に設定したい場合、~/.bashrc や ~/.bash_profile に記載

# シェル変数を環境変数に変える
VAR1="aaa";
export VAR1

# シェル変数にセットと同時に環境変数に変える
export VAR1="aaa";

# 環境変数をシェル変数に変える
export VAR1
env | grep VAR1
export -n ${VAR1}
env | grep VAR1

# script コマンドでプロセス起動時に今の環境の変数を渡す例
#  コマンド実行中のみ有効
AAA="abc"
AAA=${AAA} script /dev/null
echo ${AAA}

# 複数
VAR1="aaa" VAR2="bbb" script /dev/null
echo $VAR1 $VAR2


#  コマンド実行中のみ有効(export)
export AAA
script /dev/null

# 複数
export VAR1="aaa"
export VAR2="bbb"
script /dev/null
echo $VAR1 $VAR2


#-------------------------------------------------------------------
# sorce
#  現在のシェルプロセスでシェルを実行する（通常は子プロセスが作成される）
#  別のシェルプロセスのシェル変数を読み込ませる場合など
sorce env.sh
.env.sh # .は非推奨？


#-------------------------------------------------------------------
# 設定ファイルを読み込む順番
#  1. /etc/profile       ・・・全てのユーザ共通の設定
#  2. ~/.bash_profile　  ・・・各ユーザの[home]ディレクトリ内の設定
#    上のファイルがなければ
#     2-1. ~/.bash_login
#     2-2. ~/.profile
#  3. ~/.bashrc
#  4. /etc/bashrc
#  5. bash実行

#-------------------------------------------------------------------
# 特殊変数
#  $0	スクリプト名
#  $1 ~ $9	引数、1番目の引数を$1、2番目の引数を$2でアクセスする
#  $#	スクリプトに与えた引数の数
#  $*	全部の引数をまとめて1つとして処理
#  $@	全部の引数を個別として処理
#  $?	直前実行したコマンドの終了値（0は成功、1は失敗）
#  $$	このシェルスクリプトのプロセスID
#  $!	最後に実行したバックグラウンドプロセスID

#-------------------------------------------------------------------
# 展開
#   参考: https://manual.atmark-techno.com/armadillo-guide/armadillo-guide-2_ja-1.0.0/ch05.html
#       5.3.6. 展開
シェルでは、特定の書き方をした場合、別の文字列として置き換えられて解釈されることがあります。この置換処理を展開といいます。
展開は、ブレース展開、チルダ展開、パラメータ・変数・算術式展開、コマンド置換(左から右へ)、単語分割、パス名展開の順番で行われます。

# ブレース展開
echo a{D,C,B}e

# 展開の順序的にこれは機能しない
aaa={D,C,B}
echo a${aaa}e

チルダ(~)で単語が始まった場合、スラッシュよりも前にある文字が、チルダプレフィックスと解釈されます。有効なチルダプレフィックスの場合、チルダ展開が行われます。
チルダプレフィックスがユーザー名と一致した場合、そのユーザーのホームディレクトリに展開されます。ユーザー名が指定されない場合は、シェルを実行しているユーザのホームディレクトリに展開されます。
チルダプレフィックスが+の場合、環境変数PWDに、-の場合、環境変数OLDPWDに展開されます。(この展開は、ashではおこなわれません。)
echo ~


#-------------------------------------------------------------------
## プロセス置換
# 標準出力をファイルとして渡す
diff -y <(seq 3) <(seq 5)
# 標準出力の内容をそのまま複数のプロセスに渡す
echo 1234 | tee >(sed 's/34/xx/g') >(sed 's/34/yy/g') | sed 's/34/zz/g'

# command1の標準出力をcommand2に、標準エラー出力をcommand3に渡す
df 1> >(echo) 2> >(echo)

# ログ出力の実践例
#  標準出力、標準エラー出力をファイルにも出力する
command 1> >(tee file1) 2> >(tee file2 >&2)
#  file1に全ログ、file2にフィルタリングしたログを出力する
command 2>&1 | tee file1 | grep -i error > file2
#  file1に全ログ、file2と標準出力にフィルタリングしたログを出力する
command 2>&1 | tee file1 | grep -i error | tee file2
#  file1と標準出力に全ログ、file2にフィルタリングしたログを出力する
command 2>&1 | tee >(grep -i error > file2) | tee file1
#  標準エラー出力の行頭に[ERROR]を付けて標準出力に出力する
command 2> >(awk '{print "[ERROR]", $0}')


# パラメータ展開
#   ${#変数}      	 変数の文字数を取得する
#   ${変数:-文字列}	 変数がNULLの場合、指定した文字列を返す
#   ${変数:+文字列}	 変数がNULL以外の場合、指定した文字列を返す
#   ${変数:=文字列}	 変数がNULLの場合、変数に指定した文字列を代入する
#   ${変数:?文字列}	 変数がNULLの場合、指定した文字列を返しエラー終了する
#   ${変数#文字列}	   変数より、前方から最短一致した箇所を除外する(基本ワイルドカード組み合わせ要) /home/user/AAA/BBB/abc.log
#   ${変数##文字列}	 変数より、前方から最長一致した箇所を除外する(基本ワイルドカード組み合わせ要)
#   ${変数%文字列}	   変数より、後方から最短一致した箇所を除外する(基本ワイルドカード組み合わせ要)
#   ${変数%%文字列}	 変数より、後方から最長一致した箇所を除外する(基本ワイルドカード組み合わせ要)
#   ${変数:N}	       変数より、N文字以降の文字のみ抽出する
#   ${変数:N:X}	     変数より、N文字目からX個の文字を抽出する
#   ${変数: -N}	     変数より、後ろからN文字のみ抽出する
#   ${変数: -N:X} 	 変数より、後ろからN文字、X個の文字のみ抽出する
#   ${変数::N}	     変数より、頭からN文字のみ抽出する
#   ${変数::-N}	     変数より、後ろからN文字除外する
#   ${変数/置換前/置換後}	 変数の内容を、置換前文字列を置換後文字列に変換して返す
#                        置換文字列を省くと空白置換扱い(削除)になる  echo ${hoge/value}
#                        // とすると全ての該当箇所を置換 echo ${hoge//hoge/fuga}
#   ${変数^[文字列]}	   変数の一文字目を大文字にする(文字列を指定してる場合はその文字列のみを対象にする)
#   ${変数^^[文字列]}	 変数全体を大文字にする(文字列を指定してる場合はその文字列のみを対象にする)
#   ${変数,[文字列]}	  変数の一文字目を小文字にする(文字列を指定してる場合はその文字列のみを対象にする)
#   ${変数,,[文字列]}	 変数全体を小文字にする(文字列を指定してる場合はその文字列のみを対象にする)
#   ${!文字列*}	      指定された文字列から始まる変数を取得する

# dirパスのみ
echo ${A%/*}
# ファイル名のみ
echo ${A##*/}

# %なのか#なのかの考え方
#  ・まずマッチした箇所が除外されるということ
#  ・「#」はコメントアウトを表すので前方
#  ・1個は最短一致、2個は最長一致
#  ・ワイルドカードとの組み合わせ */ 前方を除外 /* 後方を除外
#  ${A##*/} は 前方から「/」を探索し一番遠い「/」の前方を除外
#  ${A%/*} は 後方から「/」を探索し一番近い「/」の後方を除外
#  ワイルドカード無し
#    ${A%/} は 後方から「/」を探索し一番近い「/」を除外

# 位置パラメーター (Positional Parameters) ：引数の事
#   個別の値を展開: $1、$2, …
#   すべての値を展開:$*, $@
#   位置パラメーター数を展開: $#
#   シェルスクリプトの名前を示す変数: $0
#
# 位置パラメーターの値はシェルスクリプト (コマンド) の引数を受け取るだけでなく、 スクリプト中で任意の値を設定可能
#   set -- arg1 arg2 arg3
#   echo $# ; echo $* ; echo $@
#
#   位置パラメーターを破棄：先頭の位置パラメーター値が破棄され、 以降の値を先頭にシフト
#   （shift n: nはshift数）
set -- arg1 arg2 arg3
echo $# ; echo $* ; echo $@
shift 1
echo $# ; echo $* ; echo $@

# 位置パラメーター全て破棄
shift $#
set --


# IFS: （Internal Filed Separator）： 値の間に差し込まれる文字(デフォルトはスペース ` `)
#   使い方として()で括る：他のコマンドに影響が出るため()内で完結させるため
set -- 1st 2nd 3rd 4th
(IFS='|'; echo "$*")

# 位置パラメーター値をすべて一括展開
#   ・「*」「@」と""で括るか括らないかの4パターンある
#   ・$IFS に含まれる文字 (デフォルトはスペース、タブ、改行) で分割される
# 1) 「*」""で括る場合
#   ・空白文字は削除される（trim）
#   ・展開後にワード分割されず、ひと塊の一つの値になる
set -- arg1 arg2 arg3
echo "$# [$1]"
set -- "$*"
echo "$# [$1]"
# 2) 「*」""で括らない場合
#   ・空白文字は削除される（trim）
#   ・位置パラメーター展開の後、ワード分割される
set -- 'arg1 arg2' arg3  ' arg4   arg5'
echo $#
set -- $*
echo $#
# 3) 「@」""で括る場合
#  ・単純に展開（パラメーターの数は変化せず、ワード分割や各種展開もされず、元の値と数を維持）
set -- ' foo  bar ' hoge '/bin/*sh'
echo $#
set -- "$@"
echo $#
echo "[$1] [$2] [$3]"
# 4) 「@」""で括らない場合
#  ・「2) 「*」""で括らない場合」と同様の動き

# 1)～4)使い分け
#  ・$*, $@ の使い所
#    -基本は使わない
#    -パス名展開対象の文字 (*, ? など)、 そのほかの展開対象の文字 (ブレース展開など) が含まれてないことが確実であればOK
#  ・"$*" の使い所
#    -位置パラメーターの数に依らず、そのままの値をまとめて扱いたい場合（例：ログとして位置パラメーターをすべてダンプしたい）
#  ・"$@" の使い所
#    -位置パラメーターの数と値を維持したまま扱いたい場合（例：ほかのコマンドの引数に与える場合）


# パス名展開(位置パラメーター展開の後、パス名展開される)
set -- '/bin/*sh'
echo $*



#-------------------------------------------------------------------
## tee
#   標準入力から受け取った内容を、標準出力とファイルに書き出す
#    コマンド1 | tee ファイル | コマンド2
#    コマンド | tee ファイル1 ファイル2 ファイル3……
#  -a : 指定したファイルが既に存在する場合、上書きせずに追加する



#-------------------------------------------------------------------
## mktemp
# 作業用の一時ファイル，一時ディレクトリを作成する（名前はユニーク）
# ファイルのアクセス権は，"rw-------"
# ディレクトリのアクセス権は"rwx------"になる。
#
# -d	ファイルではなくディレクトリを作成する
# -q	エラー・メッセージを表示しない
# -u	推奨されないオプション
# string	6文字の大文字「X」を末尾に含んだ文字列を指定する


#-------------------------------------------------------------------
## readlink
# シンボリックリンクのリンク先の名前を表示
#
# ファイルの“本名”を「ハードリンク」、“別名”を「シンボリックリンク」
# ※シンボリックリンクを対象にしたファイル操作は、削除（rm）などを除き、元のファイルへの操作に置き換えられる
#
# -f 正規化モードでは、リンク先をたどり、最終的なリンク先を絶対パスで表示します
#
#
# カレントディレクトリを変更（移動履歴）
pushd
popd
dirs -v」でディレクトリスタックを番号付きで一覧表示


#-------------------------------------------------------------------
## split オプション 元ファイル名 出力ファイルベースファイル名
#  オプションを指定しない場合は、1000行ごとにファイルを分割
#  分割後ファイルは自動生成される
#   -l 行数 : 分割ファイルの行数を指定する
#   -C サイズ : 分割ファイルに含める行の最大サイズを指定する
#   -d     :  接尾辞を英字ではなく数字にする（00から開始、「-d 開始番号」で変更可能）

# 例
split -d file.zip split_file
$ ls
split_file00 split_file01 split_file02 ...


#-------------------------------------------------------------------
## echo
#  echoの変数展開する時、””を付けるかどうかでdelimitterが変化する（付けないとタブとか半角スペースに変換される）
echo -e ${ARRY[0]}
echo -e "${ARRY[0]}"


#-------------------------------------------------------------------
## basname
# フルパスからファイル名のみ取得
basename /root/temp


#-------------------------------------------------------------------
## dirname
# フルパスからディレクトリ名のみ
dirname /root/temp


## chown
# -c : 変更の詳細情報
sudo chown -c importuser:importuser /home/importuser/gw_importuser.sh


#-------------------------------------------------------------------
## diff
# -yオプション：比較した結果を横並びで表示する
#    差異のある行は|でわかるようになっている
find `pwd` -name "migration_load_*.yml" | xargs -I{} diff -y {} {}.bak

# 片方を標準出力
diff -y <(grep -vf test.log /app/test.sh) /app/test.sh


#-------------------------------------------------------------------
## if
# one rainere
if [ $? == 0 ]; then echo OK; else echo NG; fi

# 条件中にワイルドカードを指定（""の中に*を入れると文字列として判別されてしまうっぽい）
#   ngケース
if [[ "test.log.7" == "test.log*" ]];then echo ok; else echo ng; fi
#   okケース
if [[ "test.log.7" == "test.log"* ]];then echo ok; else echo ng; fi




#-------------------------------------------------------------------
## nice

-n 優先度（優先度（-20～19：本文参照）を指定する）
一般ユーザーは、優先度を「0（通常）」から「19（最低）」まで指定可能（etc/security/limits.conf 変更可能）
値を指定しなかった場合の優先度は「+10」

優先度の値を“nice値”とも言います。他のプロセスに対してどのくらいniceか、という値で、大きいほど優先度が低くなります※3。

※3 今から実行するコマンドに対する優先度を下げる（CPU時間の割り当てを少なくする）ことで、他のプロセスに対してniceに（親切に）働いたことになる。



# gzipは後でゆっくり実行し先に次へ行く
nice gzip xxx.txt &

# バックグラウンドプロセス終了まで待つ
wait

# nice値を確認
ps -o pid,nice,stat,tt,user,cmd
ps -O nice
top  # NIの項目


# top
#  Z,B       Global: 'Z' change color mappings; 'B' disable/enable bold
#  l,t,m     Toggle Summaries: 'l' load avg; 't' task/cpu stats; 'm' mem info
#
#  1,I       Toggle SMP view: '1' single/separate states; 'I' Irix/Solaris mode
#
#  f,o     . Fields/Columns: 'f' add or remove; 'o' change display order
#  F or O  . Select sort field
#  <,>     . Move sort field: '<' next col left; '>' next col right
#  R,H     . Toggle: 'R' normal/reverse sort; 'H' show threads
#  ◎ c,i,S   . Toggle: 'c' cmd name/line; 'i' idle tasks; 'S' cumulative time
#  ◎ x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
#  ◎ z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
#  u       . Show specific user only
#  n or #  . Set maximum tasks displayed
#
#  k,r       Manipulate tasks: 'k' kill; 'r' renice
#  d or s    Set update interval
#  W         Write configuration file
#  q         Quit
#          ( commands shown with '.' require a visible task display window )


#-------------------------------------------------------------------
## nl
#   number line
# 1行が長い場合に改行を入れて行番号を付ける
tail -n1 test.log |sed -e 's/,/\n/g' |nl

# cut
#-b（--bytes）必要な項目をバイト数で指定する
#-d（--delimiter）区切り文字を指定する
#-f（--fields）必要な項目を項目数で指定する
cut -b1-100 test.log
# スペースで区切り、3番目の要素を抽出
cut -d" " -f 3 test.txt

#
# 標準エラー出力をパイプに流し込む方法(https://qiita.com/yukikimoto/items/7c11b1923f9337f6b3fb)
#   標準エラー出力をパイプに流し込む
コマンドA 2>&1 1>/dev/null | コマンドB
#   標準出力と標準エラー出力の両方をパイプに流し込む
コマンドA 2>&1 | コマンドB

# ログインしている人の情報を表示
# -a 全ての情報を表示
# -H ヘッダーを表示
who -aH

# コマンドの場所 ( alias 確認)
which ll

#-------------------------------------------------------------------
## sort
# -n: （--numeric-sort）：数値として並べ替える
# -r: （--reverse）：降順で並べ替える
# -t: （--field-separator）：項目の区切り文字を指定する
# -k: （--key）：キーを指定して並べ替える
# -u: （--unique-r）：重複行を省いて並べ替える
# -f: （--ignore-case-u）：大文字小文字を関係なく並べ替える

# uniq
# -c : （--countオプション）：重複した行数も表示する
# -d : （--repeatedオプション）：重複した行を表示する
# -D : （--all-repeatedオプション）：重複した行をすべて表示する
# -u : （--uniqueオプション）：重複した行は一切表示しない
# -f : （--skip-fields=Nオプション）：指定した項目以降で重複を判断する
# -w : （--check-chars=Nオプション）：指定した文字数までで重複を判断する

# sort1～５番目を昇順ソート、2項目目より後ろの項目かつ、3文字より少ない文字数で重複を判定する
cat "${TMP_LOG}" |sort -k1,5 |uniq -d -f2 -w3

# uniqの出力をタブ区切りにする
uniq -c data.txt | awk -v OFS="\t" '{print $2,$1}'



#-------------------------------------------------------------------
## xargs
#  標準入力やファイルからリストを読み込み、コマンドラインを作成して実行する
#  -t: verbose: xargsで指定されたコマンドの実行内容を表示（実行も行われる）
#  -L: 引数コマンド に一度に展開されるデータの最大値を指定(例データ数が10で-L5とした場合、コマンドは２回実行される)
#  -n: -n 1行にいくつ引数を渡すか(スペースで区切った個数)
#  -p: xargsで生成したコマンドを本当に実行するかどうかを確認する
#  -P: xargs引数コマンド の同時実行数の最大値を指定
#  -d: xargsでコマンドに渡される引数の区切り文字（デフォルトはスペース）
#  -I: 引数の位置を変更
#  -r: 標準入力が空の場合は指定したコマンドを実行しない

# 全て引数コマンドに渡して実行
seq 10 | xargs echo a

# 1つずつ引数コマンド実行
seq 10 | xargs -n1 echo a

# n個ずつ処理する
seq 10 | xargs -t -L2 echo
seq 10 | xargs -t -n2 echo

# 並列で実行（順不同になるっぽいので注意）
seq 50 | xargs -L5 -P5 echo

# 引数コマンドの途中に標準出力を渡す({}という文字列変数なので何でもよい)
seq 10 | xargs -I{} echo {} a

# xargsで渡した引数を、個別に任意の場所に代入する
seq 10 | xargs -n2 sh -c 'echo $1$0'

# if文
ls | xargs -I{} sh -c 'if [ "{}" != "test" ]; then echo "{} is not test"; fi'

# xargsで複数コマンド実行
find /efs/data/logs/ -name "test.log" | egrep ${FIND_WORD} | xargs -n1 sh -c 'echo $0 ; head -3 $0 ; tail -3 $0' | sed -e 's/,/\t/g'
find /opt/importuser_test/test*{ABC,XXX}*.yaml |xargs -t -n1 -I{} ls -l {}
find /opt/importuser_test/test*{ABC,XXX}*.yaml | \
xargs -n1 -I{} egrep 'local_path|userid' {} | \
xargs -n4 sh -c 'echo -ne $3"\t"; echo $1'


# 特定のファイルのバックアップファイルを作成する
find PATH -type f -name 'ファイル名' | xargs -n 1 -I{} cp {} {}.bk
# 特定のファイルをバックアップディレクトリにコピーする
find PATH -type f -name 'ファイル名' | xargs -n 1 -I{} cp {} バックアップ先PATH
# 特定のファイルに対してのみgrepを行う
find PATH -type f -name 'ファイル名' | xargs -n 1 -I{} grep '検索キーワード' {}


find /efs/data/logs/ -name "test.log" | egrep ${FIND_WORD} | xargs -n1 sh -c 'echo $0 ; head -3 $0 ; tail -3 $0' | sed -e 's/,/\t/g'



#-------------------------------------------------------------------
## paste 元ファイル１ 元ファイル…
#    ファイルの結合（-で標準出力を引数に可能）
#     -d(string) : 結合区切り文字 default:tab

# 標準出力の縦方向を横に結合（下の例は２行ずつ１行に結合する）
seq 10 | paste -d"\t" - -



#-------------------------------------------------------------------
## ヒアドキュメント
#   ファイルへ書き込み（>>追記、>新規（上書き））
cat << EOS >> test.log
a
a,b,c
EOS

# ヒアドキュメントを変数に代入する
QUERY=`cat <<'EOS' | sed -e "s/^\///g"
SELECT * FROM mysql.user
 WHERE User = 'root'
   AND Host = 'localhost'
EOS
`
#-------------------------------------------------------------------
## while
while read line
do
    cnt=`expr $cnt + 1`
    echo "LINE $cnt : $line"
done < /tmp/test.txt

# case
case "$VAR" in
  "a" )  echo ”処理1です” ;;
  "b" )
    echo “処理2です” ;;
  "c" )
    echo “処理3です”
    ;;
  * ) echo "想定外" ;;
esac


#-------------------------------------------------------------------
## 解凍
# zip 圧縮
zip -r xxxx.zip directory
# zip 解凍
unzip -v xxxx.zip

# tar.gz 圧縮
tar -zcvf xxxx.tar.gz directory
# tar.gz 解凍
tar -zxvf xxxx.tar.gz

# tar 圧縮
tar -cvf xxxx.tar directory
# tar 解凍
tar -xvf xxxx.tar

#-------------------------------------------------------------------
## パーミッションを数字で表示
stat /home/[username]/.ssh/config -c '%a'
ls -la | awk 'NR>1{cmd="stat "$NF" -c %a";cmd|getline c;close(cmd);print c,$0}'


#-------------------------------------------------------------------
## mkdir
#  -pオプション（--parents）：ディレクトリが存在しなければ作成する
#  -v verbose
#  -m パーミッション
# ディレクトリ階層作成
mkdir -pvm 777 ~/test/{AAA,BBB}

mkdir -vm 777 /efs/data/tmp/test

# 文字コード改行コード確認
nkf --guess　ファイル名
file -i　ファイル名

od -c [ファイル名]

#-------------------------------------------------------------------
## yqコマンド
# -cで一行表示
cat test.yml | yq -c '.in.parser.columns'
# 配列の特定箇所を抽出（[]は配列を表す）
cat test.yml | yq -c '.in.parser.columns[0]'
cat test.yml | yq -c '.in.parser.columns[3:]'
# 条件抽出
cat test.yml | yq -c 'map(select( .["age"] > 30))' | yq '.[].name'
# 要素配列数の取得
cat test.yml | yq -c '.in.parser.columns' | yq length

## jq
## jq - commandline JSON processor [version 1.5]
# Usage: jq [options] <jq filter> [file...]
#
#   jq is a tool for processing JSON inputs, applying the
#   given filter to its JSON text inputs and producing the
#   filter's results as JSON on standard output.
#   The simplest filter is ., which is the identity filter,
#   copying jq's input to its output unmodified (except for
#   formatting).
#   For more advanced filters see the jq(1) manpage ("man jq")
#   and/or https://stedolan.github.io/jq
#
#   Some of the options include:
#    -c           compact instead of pretty-printed output;
#    -n           use `null` as the single input value;
#    -e           set the exit status code based on the output;
#    -s           read (slurp) all inputs into an array; apply filter to it;
#    -r           output raw strings, not JSON texts;
#    -R           read raw strings, not JSON texts;
#    -C           colorize JSON;
#    -M           monochrome (don't colorize JSON);
#    -S           sort keys of objects on output;
#    --tab        use tabs for indentation;
#    --arg a v    set variable $a to value <v>;
#    --argjson a v set variable $a to JSON value <v>;
#    --slurpfile a f      set variable $a to an array of JSON texts read from <f>;
#   See the manpage for more options.

# tsv形式に変換（-rでダブルクォーテーションを外す）
cat ${job_info_list} | jq -r '[.ctime, .mtime, .job_id,.job_name] |@tsv' | sort


#-------------------------------------------------------------------
## comm
#    ※ソート済みのテキストファイルを比較する
#   -1	1列目（ファイル1のみに含まれる行）を出力しない
#   -2	2列目（ファイル2のみに含まれる行）を出力しない
#   -3	3列目（両方のファイルに含まれる行）を出力しない
#   --check-order	全ての行の組み合わせが一致していても、ソートされているかどうかを確認する
#   --output-delimiter=文字列	列を区切る文字列を指定する（デフォルトはTAB
file1="test1.txt"
file2="test2.txt"

# ファイル1のみに存在するレコードを出力
comm -23 ${file1} ${file2} | wc -l
# ファイル2のみに存在するレコードを出力
comm -13 ${file1} ${file2} | wc -l
# ファイル1,2 に共通して存在するレコードを出力
comm -12 ${file1} ${file2} | wc -l
# ファイル1,2に共通しないものを出したい
comm -3 ${file1} ${file2} | wc -l

#-------------------------------------------------------------------
### 正規表現全般
http://gimite.net/help/devas-ja/all_regex.html

# 各行の一番最初にヒットした1文字だけを置換する
(^.*?)(: )
\1\t

# ファイルパスの場合分け
tail ../202001{28,29,30}/test.log

#-------------------------------------------------------------------
## tree
#  treeコマンドは別途インストールが必要なためsedで代替する
pwd;find . | sort | sed '1d; s/^\.//; s/\/\([^/]*\)$/|-- \1/; s/\/[^/|]*/|  /g'

#-------------------------------------------------------------------
## sed
#  sedの処理フロー ※改行コードを置換などは簡単に出来ない  perl -pe 's/\n/ /g' でやればよい
#   一行メモリ(pattern space) に読み込む (改行は含まない)
#   コマンド( s/\n/ /g などのこと)たちの実行
#   メモリ(pattern space) の出力(この時再度改行が追加される)

#  先頭行に追加(iで直接編集)
sed -i "1s/^/abc\n/" test.txt
#  -i[string]でバックアップ（ファイルパスprefixへstringを追加する）
sed -i.buckup "1s/^/abc\n/" test.txt

# 先頭行に文字列を追加
sed -e "1s/^/{% include \'commons\/env\' %}\n/" test.yml

# 先頭2行目に追加
sed "2s/^/exec: {max_threads: 2}\n/" migration_load_test_ad.yml

# 文字列をを変数に変更
sed -e "s| apikey: .*| apikey: {{ test_api_key }}|g" load_test_test_log.yml

# 先頭N行の削除
sed '1,2d' in.txt

# ＃から始まる行を削除
sed '/^#/d' input.txt

# 正規表現のサンプル
echo ${str##*/} | sed -E "s/^.+${TYPE}_.{8}(..).+/\1/g"

sed -e "s| apikey: .*|apikey: {{ test_api_key }}|g" load_test_test_log.yml

apikey: {{ test_api_key }}
sed -e "s|path_prefix: .*|path_prefix: $1|g" -e "s| table: .*| table: $2|g"

# バックスラッシュ(\)を使用して記述する sedの区切り文字はスラッシュ(/)のままの場合は、バックスラッシュを用いて記述します。
$ echo "/123/" | sed "s/\//abc/g"

# \n という文字列を置換する
sed -E "s/\\\n/\n/g"

#-------------------------------------------------------------------
## perl
# 改行の置換
perl -pe 's/\n/ /g'


#-------------------------------------------------------------------
## head と tail
# 先頭3行を表示
seq 10 | head -n3
# 末尾3行を表示
seq 10 | tail -n3
# 末尾から指定した行数を除いて表示
seq 10 | head -n-3
# 先頭から数えて指定した行以降を表示（+2: 2行目を含みそれ以降を表示）
seq 10 | tail -n+3

# 指定行範囲の抽出
START=1
END=4
seq 10 | head -n-${END} | tail -n-$((${END}-${START}+1))

# こっちのほうがいい
seq 10 | sed -n 2,3p


# tee: 標準出力とファイルへ同時出力
# -a : ファイルに追記
find . | tee ./test.txt


#-------------------------------------------------------------------
## du
# Mbyteで -d 深さ1 を集約し、昇順ソート
sudo du -m -d1 / | sort -n

#-------------------------------------------------------------------
## date
# フォーマット
date "+%Y%m%d%H%M%S"
# unixtime を日付へ変換
date --date "@1558664000"
# unixtime表示
date +%s
# 任意の日付をunixtimeへ変換
date --date='1970-01-01 09:00:00' +%s
# 任意の日付の1か月後（※月末日の1か月後は月によっては2か月後になってしまうので注意）
date '+%Y-%m-01' -d "`date --date='2018-1-01' +%Y-%m-01` 1 months"


#-------------------------------------------------------------------
## time
# real	プログラムの呼び出しから終了までにかかった実時間（秒）
#       プログラムがCPUを使った時間 + ネットワークI/Oの待ち時間 + ディスクI/Oの待ち時間
# user	プログラムがCPUを使った時間
# sys	  プログラム以外（OS）がCPUを使った時間
time sleep 5


#-------------------------------------------------------------------
## ファイルの上書き
diff -y a.txt <(cmd a.txt)
echo "$(cmd a.txt)" > a.txt


#-------------------------------------------------------------------
## ファイルの上書き2
STR=$(grep -vf del_list.txt /app/test.sh)
diff -y /app/test.sh <(echo "${STR}")
echo "${STR}" > /app/test.sh

#-------------------------------------------------------------------
## grep
# -iオプション：大文字と小文字を区別せず検索する
# -Eオプション：拡張正規表現で検索を行う
# -eオプション：一致処理に指定した正規表現を使う
# -vオプション：一致しないものを検索する
# -nオプション：検索結果に行番号を表示する
# -lオプション：検索結果にファイル名のみ表示する
# -hオプション：検索結果にファイル名を表示しない
# -oオプション：検索結果に一致した文字を表示する
# -Cオプション：検索結果に一致した箇所から前後に指定した行数表示する
# -rオプション：ディレクトリ内も検索対象とする
# -Lオプション：検索した結果、該当しなったファイルを表示する
#  ※ 正規表現を使う場合、-eより-Eの方が最近のエディタ等で使用可能な表現が使えるためおすすめ
#  ファイルフォーマット確認（例：2018-11-01_2018-11-30_user_info.tsv）
echo "test.tsv" | grep -E "20[0-9]{2}\-[0-9]{2}\-[0-9]{2}_"

# -A 後の行を表示する
# -B 前の行を表示する
# -[num] 該当行の前後num行表示
grep -A 3 -n abcd smb.conf

# grepで条件判定
if `echo "${ARR_LOG[i]}" |egrep -q 'Started.|Completed.'`; then
  echo "OK"
fi




#-------------------------------------------------------------------
## find
# -mmin	ファイルのデータが最後に修正された日時（分指定）
# -mtime	ファイルのデータが最後に修正された日時（日指定）
# -amin	ファイルのデータに最後にアクセスされた日時（分指定）
# -atime	ファイルのデータに最後にアクセスされた日時（日指定）
# -cmin	ファイルのデータとステータスが最後に修正された日時（分指定）
# -ctime	ファイルのデータとステータスが最後に修正された日時（日指定）
# -type d ： ディレクトリを対象とし検索する
# -type f ： ファイルのみを対象とし検索する
# -maxdepth 2 : 検索対象の深さ

# 現在から3日前まで（現在時間～72時間前）
find ./ -mtime -3
# 3日前（72時間前～96時間前）
find ./ -mtime 3
# 過去から3日前まで（72時間前～過去）
find ./ -mtime +2
# ※ (2+1)日前以降

# 現在から3日前まで（12月24日24時00分～72時間前）
find ./ -daystart -mtime -3
# 3日前（12月21日24時00分～24時間前）
find ./ -daystart -mtime 3
# 過去から3日前まで（12月21日24時00分～過去）
find ./ -daystart -mtime +2
# ※ (2+1)日前以降

# 12時間前まで（現在時間～12時間前）
find ./ -mtime -0.5



#-------------------------------------------------------------------
## ls
# long-iso 形式
ls -la --time-style=long-iso
find ./raw/ -type f | sort | xargs ls -l --full-time
find ./raw/ -type f | sort | xargs ls -l --time-style=+%Y-%m-%d\ %H:%M:%S


#-------------------------------------------------------------------
## touch
#  ファイルタイムスタンプを変更
touch --date="2019-01-16 16:21:22" test.tsv

#-------------------------------------------------------------------
## less
# -N 行番号
# -S 折り返さない
# +F tail -fのような
#    ctrl c で解除、大文字Fで設定を切り替えられる
less -SN +F test.log

#-------------------------------------------------------------------
## stat
#   ファイルの日付属性の表示（Access、Modify、Change）
stat test.tsv

#-------------------------------------------------------------------
## ps
#   -e：全てのプロセスを選択する。-A と等しい。
#   -o：ユーザー定義フォーマット。
#   lstart：コマンドが起動された時刻。
#   pid：プロセスのプロセス ID 番号。
#   args：文字列の引き数がついたコマンド
# コマンドが起動した時間を確認する
ps -eo lstart,euser,pid,ppid,tty,args


#-------------------------------------------------------------------
## pstree
#  -a	--arguments	コマンドラインの引数も表示する
#  -p	--show-pids	プロセスIDを表示する
#  -l	--long	長い行で表示する
#  -s プロセスID	--show-parents=プロセスID	指定したプロセスの親を表示する
#  -h	--highlight-all	カレントプロセスとその先祖のプロセスを強調表示する
#  -H プロセスID	--highlight-pid=プロセスID	指定したプロセスとその先祖のプロセスを強調表示する
pstree -apl

#-------------------------------------------------------------------
## array 配列（bashは配列はなく、無理やり実現させる方法）
# 配列用意
array=()
# 先頭に追加
array=(3 "${array[@]}")
# 末尾に追加
array=("${array[@]}" 4)
array=("${array[@]}" "${PATH_TO%/}/$(basename ${ARRY[i]#/})")
# データの先頭要素を取り出す（破壊的操作）
array=("${array[@]:1}")
# データの末尾要素取り出す（破壊的操作）
array=(${array[@]:0:((${#array[@]}-1))})
# 配列参照
echo "${array[@]}"
echo "${array[0]}"
# 配列のデータから任意の要素を削除
unset array[1]
# 配列要素数
echo ${#arr[@]}
echo $((${#arr[@]}-1))

# インクリメント （letを使う）
i=0
for e in ${array[@]}; do
    echo "array[$i] = ${e}"
    let i++
done

# ファイルを配列にする
ARRY=(`cat ${TMP_LOG} | xargs`)



#-------------------------------------------------------------------
### mapfile
# 配列変数に代入
# -t: 配列要素の最後に入る区切り文字を取り除く
# -s: 最初のn行を切り捨てる
# -n: 標準入力から代入する行数を指定
# -O: 配列変数の開始要素番号を指定（デフォルト０）
# -u: ファイルディスクリプタを指定して入力
# -C: コールバック

# ファイルを配列にする
mapfile -t ARRY < ${TMP_LOG}

# 最後に改行を入れると配列要素として追加されてしまうため、空白行を削除してセット
mapfile -t ARR_RECV < <(sed -e "/^$/d" ${RECOVERY_LIST})


# 変数を配列にする
AAA=`cat <<'EOS'
123,aaa,1111
234,bbb,22222
345,ccc,3333
EOS
`

mapfile -t ARRY < <(echo "${AAA}")
for i in "${!ARRY[@]}"; do
	echo ${ARRY[i]}
done

# egrepの結果を配列へ
mapfile -t ARRY < <(egrep "^activity" ${TMP_LOG})

# csvを配列に
TEXT='BBB,データ,3456'
arr=( `echo $TEXT | tr -s ',' ' '`)
echo ${arr[0]}
echo ${arr[1]}
echo ${arr[2]}

# 配列要素全てをfor文で順番に処理
i=0
for e in ${arr[@]}; do
  echo ${arr[$i]}
  let i++
done

# C言語風にも書ける
i=0
for ((i = 0; i < ${#arr[@]}; i++))
do
    echo "$i => ${arr[$i]}"
done

# !arrのように書くとindexが展開される
array=()
mapfile -t array < <(seq 10)
for i in "${!array[@]}"
do
  echo -e "${array[i]}\t${array[i-1]}\t${array[i+1]}"
done



#-------------------------------------------------------------------
## awk
# カラム数カウント(tsv)
head -n1 test.tsv | awk -F '\t' '{print NF}'

# 計算
echo $((1+1))
echo $((`head -n1 test.tsv | awk -F '\t' '{print NF}'` + `head -n1 test.tsv | awk -F '\t' '{print NF}'`))

# csv 指定カラム条件抽出 prinf $0 は全ての列を出力
cut -d"," -f2- test.csv | awk -F',' '$1!=NULL {print $0}' |wc -l

# 文字列検索の場合""で括る
echo "${AAA}" | awk -F'/' '$2=="idfa" {print $0}'

# デリミタをタブ区切りで指定する場合
awk -F"\t" '$4=="activity" {print $0}'

# awkの中でshellの変数を使う（-v を使う）
awk -v t=${type} -F"\t" '$4==t {print $0}'

# split後、後ろから指定フィールド目を取得
dir="/home/test/user"
echo "${dir}" | awk -F'/' '{print $(NF-1)}'

#-------------------------------------------------------------------
## bzip2
# 指定したファイルをBurrows-Wheeler変換というアルゴリズムを用いて圧縮します。「gzipコマンド」や「compressコマンド」で利用されるアルゴリズムより効率のよい圧縮率で圧縮できます。
#
#-c	圧縮結果を標準出力に表示します。
#-d	圧縮されたファイルを展開します。bunzip2コマンドと同等。
#-f	ファイルが既に存在する場合は上書きします。
#-t	圧縮テストを実行します。圧縮は行われません。
#-v	圧縮率を表示します。

# 解凍
bzip2 -d file.bz2

# 圧縮率確認
bzip2 -v file.bz2


#-------------------------------------------------------------------
## alias
#  alias一覧を表示
alias
#  登録
#    aliasは、ターミナルの画面を閉じる、ログアウト、コンピュータのシャットダウンでリセットされる
alias ll='ls -alF --color=auto --time-style=+%Y-%m-%d\ %H:%M:%S'
# 永続的に登録したい場合、.bashrcに書き込んでおく
~/.bashrc
# 登録解除
unalias test_command

## crontab
# -l : cronの一覧を表示（ログインユーザの情報のみ）
crontab -l

# cron実行情報を確認
sudo grep "\.sh" /var/log/cron

#-------------------------------------------------------------------
## screen [ -options ] [ cmd [ args ] ]
#  screen は呼び出されると仮想端末を 1 つ生成し、その中でシェル (または指定されたコマンド) を実行する
#  アタッチ：仮想端末のセッションに接続
#  デタッチ：使用中の仮想端末を切断（セッションは残る）
#
#  仮想端末で処理したい作業が終わり、仮想端末をそのまま残したい場合は、現在開いている仮想端末をデタッチ


# screenの後に実行したいコマンド
screen -S test123 vmstat -t 3
# 直後にデタッチすれば、閉じてしまってOK（コマンドが終了すれば仮想環境も閉じられる）

# 状況を見たい場合はアタッチすればよい
screen -r

screen -S my_vm_window vmstat -t 3 仮想端末上のシェル上で指定コマンドを実行
screen -ls : 仮想端末を確認
ctrl + a -> d 仮想端末を切断（デタッチ）
screen -d [pid] 仮想端末を切断（デタッチ）
screen -r [pid] 仮想端末に接続（アタッチ）
ctrl + a -> k 仮想端末を閉じる 仮想端末上でctrl + D とかexitでも同様

ctrl + a S or | : 画面を分割
ctrl + a Tab : 画面を移動
ctrl + a X : 現在の画面を閉じる
ctrl + a c : 新規仮想端末を開く
ctrl + a n : 次画面
ctrl + a p : 前画面
ctrl + a Q : 分割ウィンドウ閉じる

-S セッション名  セッションに意味のある名前を付けることができる
-ls
-d セッション中にscreenコマンドに-dオプションをつけるとデタッチ
-r [screenname or number] : アタッチ
-A すべてのウィンドウの大きさを現在の端末の大きさに合わせる
-m screen に $STY 環境変数を無視させる。 "screen -m" とすると、この screen が他の screen セッションから呼び出されたかどうかに関わらず、 新たなセッションの生成が強制される。
-d -m screen を "detached" モードで起動する。新たなセッションが生成されるが、 そのセッションへアタッチしない。 これはシステムのスタートアップスクリプトで便利である。


# detachされたセッションを削除する
screen -S 24015 -X quit
screen kill 24015

# detach済みscreenの一括削除
echo rm -rfv /var/run/screen/S-${USER}/*

# detachモードでscreenを作る
screen -d -m -S testt10

# 他のセッションをdetachする
screen -d 24015

# attachされたscreen セッションへのアタッチを行う (マルチディスプレイモード)
# Attached画面に再接続することもできますscreen -x
# 端末を誰かと共有したり、彼らが何をしているのかを見たり確認したり
screen -x 15812

# detachモードで立ち上げコマンド実行（atatchしないためfor文中にscreen可能）
#   引数コマンドの実行が完了し次第scrennは破棄される
for i in $(seq 5); do
  screen -dmS test_screen_$i sh -c "echo count:$i; sleep 60"
done

screen -ls

# 別のセッションへコマンドを送り更に実行する(\015は改行)
screen -S 4667 -X stuff 'echo aaa'`echo -ne '\015'`
screen -S 4667 -X stuff '
echo aaa
echo bbb
'`echo -ne '\015'`

# 指定のセッションの入力バッファに文字列を配置
screen -S 4667 -X stuff '{
echo aaa
echo aaa
echo aaa
'

# 実行（改行を入力バッファに配置）
screen -S 4667 -X stuff '}'`echo -ne '\015'`

# screenプロセスが死んだ場合にごみを消す（kill -9とかで削除した場合も）
screen -wipe

# 疑似ターミナル作成（sshログインユーザからsuした時screenは元のユーザのターミナルを見に行くが権限がないと怒られるためsuした後に疑似ターミナルをscriptコマンドで立ち上げておく。exitすれば残らない）
script /dev/null
screen -S screen_test sh -c '
echo abc
sleep 3
'



#-------------------------------------------------------------------
## cp
#  -r オプション：ディレクトリごとコピー（再帰的）
#  -p 所有者や権限などを保持してコピー
#  -P（--parents）：ディレクトリ構造ごとコピーする
cp -pvr /home/testuser/config /efs/data/tmp/test



#-------------------------------------------------------------------
## nohup
# nohupはターミナルの接続が切れた場合にハングアップシグナル（SIGHUP, HUP）という強制終了の命令を無視する。
# 崩した説明↓
#   &でバックグラウンドで実行するが、
#   現在のプロセスにぶら下がる感じで実行される
#   つまりログアウトすると、子供たちにハングアップシグナル(HUP)が送られシェルが終了する
#   nohupコマンドはその名の通り、hupを送らないコマンド

# stdout/stderrがカレントディレクトリの「nohup.out」へ書き込まれる(追記書きされる)
nohup vmstat 3 &

# 出力先を変更したい場合
nohup echo abc > abc.log &

# 順番に複数コマンドを実行したい場合
nohup sh -c 'echo aaa; echo bbb' &

# バックグラウンドに状況を確認したい
tail -Fn0 nohup.out

# バックグラウンドに状況を確認したい2
jobs -l
fg %1
ctrl + z # 一時停止される

jobs -l
bg       # 再度バックグラウンドへ

```
