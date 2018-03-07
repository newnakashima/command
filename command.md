# Linux基本コマンドまとめ
例文で、頭に$とか#がついているのはコマンドラインでの実行例。
ついてないやつはシェルスクリプトの例文。

## alias
コマンドのエイリアスを設定する。

```
$ alias vim='nvim'
```

エイリアスを設定してても``\vim``などとバックスラッシュをつけてやれば元のコマンドを使うことができる。

## case
switch case 的なことができる。

```
case $1 in
    0)
        echo hoge ;;
    1) 
        echo fuga ;;
    *)
        echo piyopiyo ;;
esac
```

書き方が独特なので注意する。
*)はdefaultみたいなやつ。

## cat
ファイルの内容を表示する。lessとかmoreとかとは違って一気にファイルの中身を表示する。

パイプで繋いでgrepとかする場合はlessとかよりこれを使うことが多い。

```
# access.logの内容から'error'を含む行だけ抽出する。重複行は出力しない。
$ cat access.log | grep 'error' | sort | uniq
```

## chmod
ファイルの権限を変更する。

```
$ chmod 755 script.sh
$ chmod o+x script.sh
```

### 数字で指定する場合

| 数字 | 権限    |
| ---  | ---     |
| 4    | read    |
| 2    | write   |
| 1    | execute |

- 7の場合は4:read + 2:write + 1:executeなので、読み書き実行の権限を意味する。
- 5の場合は4:read + 1:executeなので、読み込みと実行の権限がある。
- 6の場合は4:read + 2:writeなので、読み書きの権限がある。

所有ユーザー、所有グループ、その他のユーザー、の順に権限を指定する。  

754の場合、

- 所有ユーザーは読み書き実行
- 所有グループは読み込みと実行
- その他のユーザーは読み込み

の権限を持つということになる。

### 記号で指定する場合

| 誰が | 意味             |
| ---  | ---              |
| u    | 所有ユーザー     |
| g    | 所有グループ     |
| o    | その他のユーザー |
| a    | すべてのユーザー |

| オペレータ | 意味           |
| ---        | ---            |
| +          | 権限を加える   |
| -          | 権限を削除する |

| 権限 | 意味         |
| ---  | ---          |
| r    | 読み込み権限 |
| w    | 書き込み権限 |
| x    | 実行権限     |

他にも使用可能な記号はあるけど割愛。

詳しくは``man chmod``すること。

``chmod u+x filename``は、所有ユーザーに実行権限を加える、ということになる。

## crontab
cronの設定をする。

```
$ crontab -e
```

``-e``オプションでcrontabファイルを編集できる。

``-l``オプションでcrontabファイルの内容を表示。

TODO: crontabファイルの書き方

## date
システム日付を設定する。引数なしの場合はシステム日付を表示する。

```
# date 112218302020
```

上記ではシステムクロックが2020年の11月22日18時30分に設定されている。月日時分（年.秒）の順に指定する。

日付をフォーマットして出力することもできる。

| 指定子 | 値   |
| ---    | ---  |
| %Y     | 年   |
| %m     | 月   |
| %d     | 日   |
| %H     | 時   |
| %M     | 分   |
| %a     | 曜日 |

```
$ date "+%Y年%m月%d日 %H:%M%"
```

フォーマット指定子を使うときは+を頭につける。

詳しくは``man date``参照。

## declare
定義済みの変数、関数を表示する。

```
# 定義済みの関数だけ表示
$ declare -f 
```

## env
環境変数を表示する。

## for
ループ処理できる。

```
for number $(seq 1 10)
do
  echo $number
done
```

## free
メモリ使用量を表示する。

## function
関数を定義する。

```
function echoecho() {
  echo $1
  echo $2
}
echoecho hoge fuga

# 結果は下記のようになる
hoge
fuga
```

## grep
ファイルの中身から文字列を検索して一致した行だけ表示する。初心者はgrepとfindが使えるようになるとシェルがわかった気になれる。

```
$ cat fuga.txt
aaaa
hoge
bbbb

$ grep "hoge" fuga.txt
hoge
```

ソースコードから全文検索的なことをしたい場合は下記。

```
$ grep -nr "hoge" ./*
```

-nは行数を表示。-rはディレクトリを再帰的に検索。-eを使えば正規表現で検索できる。-Eだと拡張正規表現が使える。-vをつけると正規表現に一致しない行で絞り込める。

-Eは、代わりにegrepとコマンド打っても良い。fgrepは特殊文字を使わず文字通りに検索する。

## groupadd
グループを作成する。

```
# groupadd fuga
```

## groupdel
グループを削除する。

```
# groupdel fuga
```

## groupmod
グループの設定を変更する。

fugaグループの名前をpiyoに変更する例。

```
# groupmod -n piyo fuga
```

## hwclock
ハードウェアクロックを設定する。``hwclock -r``で実行するとハードウェアクロックの現在時刻を表示する。

- ``hwclock -w``でシステムクロックの値をハードウェアクロックに書き込む
- ``hwclock -s``でハードウェアクロックの値をシステムクロックに書き込む

## iconv
文字コードを変換する。

```
# 使用可能な文字コードを表示
$ iconv -l

# UTF8からSJISに変換
$ iconv -f utf8 -t SHIFT-JIS sourcefile.sh > converted.sh
```

## id
ユーザーが所属しているグループを表示する。

## if
if文を作れる。条件分岐で処理を変えることができる。

```
if [ $? -eq 0 ]; then
  echo success!
else
  echo failed...
fi
```

else if 的なことをしたい場合は``elif``と書く。

条件文の中身については``test``の項を参照。

## less
ファイルを一画面ずつ表示する。

Emacsっぽい操作もVimっぽい操作も両方使える。

``less +F /path/to/logfile``とかすることで``tail -f``みたいなこともできる。

色付きで表示したい場合は``less -r``するとよい。

## let
数値計算をする。

```
$ hoge=10
$ let fuga = hoge + 10
$ echo fuga
```

シェルでは>とか>>とか&とかの文字が特殊文字なので、ビット演算をするときはエスケープしないといけない。

```
$ let 'piyo=2<<3'
$ echo $piyo
16
```

## locale
ロケール設定を確認する。現在のロケール変数にどんな値が入ってるか見ることができる。

- ``-a``でロケール一覧を表示する
- ``-m``で文字コード一覧を表示する

ロケールを一時的に変えつつコマンド実行することもできる。

```
$ LANG=C man ls
```

上記はmanページを英語で表示する。``LANG=C``のCは、プログラムのデフォルト言語という意味らしい。

## nkf
ネットワーク用漢字コード変換フィルタ、略してnkf。

最初からは入ってないと思うのでyumとかaptとかでインストールする。

```
$ nkf -g filename.txt
```

## ntpdate
ntpを使って時刻を合わせる。

```
# ntpdate ntp.jst.mfeed.ad.jp
```

これも最初は入ってないと思うのでyumとかaptでインストールする。

で、文字コードを自動判別してくれるので便利。他にも色々機能があってすごそう。

## passwd
パスワードを変更する。ユーザー名を省略すると自分のパスワードを変更する。

- ``-l``オプションでユーザーをロックする。
- ``-u``オプションでロックを解除する。

ubuntuではrootユーザーもパスワード設定しないとログインできない。

## printenv
環境変数を表示する。
引数に変数を指定するとその変数の値だけを表示する。

## read
標準入力から入力を受け取る。

```
read input
echo $input
```

while文に組み合わせることもできる。

```
$ cat lines.txt
aaa
bbb
ccc
```

echolines.sh

```
number=1
while read line
do
  echo $number $line
  let number=number+1
done < $1
```

```
$ ./echolines.sh lines.txt
1 aaa
2 bbb
3 ccc
```

## rm
ファイルを削除する。
ディレクトリを削除したい場合は``rm -rf dirname``とかをよくやるが、プロンプトも何もなしにファイルが消えてしまうので注意。
-r はrecursiveの意味で、再帰的にファイルを削除する。
-f はforceの意味で、強制的にファイルを削除する。

## rmdir
ディレクトリを削除するが、空っぽのディレクトリしか削除できない。

代わりに``rm -rf dirname``をよく使う。

## set
シェル変数と環境変数を表示する。

-o をつけるとシェルのオプションが有効になる。

```
# vi風のキーバインドにする

# bashの場合
$ set -o vi

# zshの場合
$ bindkey -v
```

``set -o``するとオプション一覧を表示できる。

## source
シェルスクリプトを読み込む。

```
$ source ~/.bashrc
```

設定ファイルを追加で読み込むときとかに使う。

ただ、.bashrcを読み込み直したい、という用途には向かない。内容がどんどん追加で読み込まれるので、$PATHの中身とかが半端なく長くなってしまう。

設定を読み込み直すときは、

```
$ exec $SHELL -l
```

と打つことが多い。

## tac
ファイルの中身を後ろから順に出力する。catの反対なのでtac。多分。

## test
様々な引数をとることで真か偽かの値を得ることができる。

```
# ~/.bashrcがファイルかどうかチェックする
$ test -f ~/.bashrc

# 結果をしらべてみる。0なら真。1なら偽。
echo $?
```

ちなみに$?は直前のコマンドの返り値が格納されている変数。

シェルでは返り値が0の時が正常という意味。1は異常な結果のとき。それ以外にも255まで返り値を設定できる。**終了ステータス**などと呼ぶ。

testは``[ オプションと引数 ]``という書き方もできる。

### testの色々な使い方

| 例                                     | 意味                                                   |
| ---                                    | ---                                                    |
| ``test $1 -eq $2``                     | $1と$2が等しい数値である                               |
| ``test $1 -ne $2``                     | $1と$2が異なる数値である                               |
| ``test $1 -lt $2``                     | $1が$2より小さい(**l**ess **t**han)                    |
| ``test $1 -le $2``                     | $1が$2以下(**l**ess or **e**qual)                      |
| ``test $1 -ge $2``                     | $1が$2以上(**g**reater or **e**qual)                   |
| ``test $1 = $2``                       | 文字列$1と文字列$2が等しい                             |
| ``test $1 != $2``                      | 文字列$1と文字列$2が等しくない                         |
| ``test -n $str``                       | 文字列$strの長さが0より大きい                          |
| ``test -z $str``                       | 文字列$strの長さが0                                    |
| ``test -f ~/.bashrc``                  | .bashrcがファイルである                                |
| ``test -d ~/.ssh``                     | .sshがディレクトリである                               |
| ``test -r ~/.bashrc``                  | .bashrcが読み込み可                                    |
| ``test -w ~/.vimrc``                   | .vimrcが書き込み可                                     |
| ``test -x /bin/bash``                  | bashが実行可能                                         |
| ``test -s test.txt``                   | test.txtのサイズが0より大きい                          |
| ``test -L link``                       | linkがシンボリックリンクである                         |
| ``test -e ~/.bash_profile``            | .bash_profileが存在する                                |
| ``test file1 -nt file2``               | file1の更新日がfile2より新しい(**n**ewer **t**han)     |
| ``test file1 -ot file2``               | file1の更新日がfile2より古い(**o**lder **t**han)       |
| ``! test -n $str``                     | 文字列$strの長さが0より大きければ偽(!は論理否定)       |
| ``test -n $hoge = abc -a $fuga = def`` | $hogeがabcと等しく、かつ$fugaがdefと等しい(-aは論理積) |
| ``test -n $hoge = abc -o $fuga = def`` | $hogeがabcと等しいか、$fugaがdefと等しい(-oは論理和)   |

## tzselect
システムで使用するタイムゾーンを対話的に設定できる。

## unalias
エイリアスを解除する

```
$ unalias vim
```

## unset
定義済みの変数、関数を削除する。

```
$ hoge='fuga'
$ echo $hoge
fuga

$ unset hoge
$ echo $hoge

# 何も出力されない
```

## useradd
ユーザーを追加する。

```
# useradd hoge -m
```

``-m``をつけるとホームディレクトリを勝手に作ってくれる。

日常用途でサーバーにユーザーを追加するだけなら``-m``覚えておけば多分十分。
あと、``-s path/to/shell``オプションを付けることでそのユーザーの使用シェルを設定できる。

ユーザーを追加するだけではパスワードが設定されてなくてログインできないので、``passwd``コマンドを使ってパスワードを設定する。

既に存在するユーザーの設定を変更するには``usermod``コマンドを使う。

## userdel
ユーザーを削除する。

```
# userdel -r hoge
```

``-r``オプションでホームディレクトリも一緒に消す。

## usermod
ユーザーの設定を変更する。

オプションはほとんど``useradd``と一緒だが、違うのは``-L``オプションと``-U``オプション。

```
# usermod -L hoge
# exit
$ su - hoge
パスワード:
su: 認証失敗
```

- ``-L``でユーザーをロックする。sudo権限からでないとログインできなくなる。
- ``-U``でユーザーのロックを解除する。

## while
while文を書ける。

```
number=0
while [ $number -lt 10 ]
do
  echo $number
  let number=number+1
done
```
