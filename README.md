# cle
CLipboard Editor.

# Overview
クリップボードにコピーした文字列を素早く編集するツールです。

# Requirement
- Windows 10+
- Python 3.7+

# How to use

## 基礎その1 - cle.py とメソッド（加工処理）の関係
以下を実行すると、cle が持つメソッド xxxx を呼び出します。

```
$ python cle.py xxxx
```

たとえば「クリップボード中の文字列を昇順ソートする sort_asc」を実行したい場合、以下のようにします。

```
$ python cle.py sort_asc
```

メソッドは cle.py にてグローバルで定義します。以下は sort_asc の例です。

```python
def sort_asc(s):
    lines = Util.str2lines(s)
    newlines = sorted(lines)
    newstr = Util.lines2str(newlines)
    return newstr
```

ルールは以下に乗っ取ります。

- 引数は s 一つです
    - String の略です
    - クリップボードにコピーされた文字列が入っています
- Util クラスを使えば加工しやすいと思います
    - たとえば1行1要素のリストにしたい場合、`Util.str2lines()` が使えます
- 加工し終えた文字列を return してください

上記ルールに従えば、他のメソッドをつくることもできます。

## 基礎その2 - cle.py が持つメソッドの一覧を知る
`--list` オプションを与えます。

```
$ python cle.py --list
is_mac
zenkaku2hankaku
tab2space
remove_spaces
remove_duplicate_lines
remove_blank_lines
crlf2space
escape_tag
sort_asc
sort_desc
reverse_order
from_powerpoint_ppt
mdquote
mdlinks
prependmdlist
prependmdlist_with_indent
amazon_url_simplify
mask
pretty_jsonstring
minify_jsonstring
onenote2scb
remove_scrapbox_link_bracket
```

## cle の各メソッドを簡単に使えるようにする
基礎その1とその2を使って適当に工夫してください。

以下は筆者が行っている方法です。

- [peco](https://github.com/peco/peco) というインクリメンタルサーチツールを使う
- [AutoHotkey](https://www.autohotkey.com/) というホットキーツールを使う
- (下記 xargs の実行には Git For Windows など Linux ツールが必要)
- 以下のように組み合わせる
    - 1: pecoを使って「メソッドの一覧からインクリメンタルサーチで選ぶ」「選んだメソッドを実行する」コマンドラインをつくる
    - 2: 1 を、Autohotkey から一発で呼び出す

### cl.bat
peco を使ったコマンドライン。

```bat
@echo off

setlocal

set cle_path=D:\work\github\stakiran\cle.py
python %cle_path% -l | peco | xargs python %cle_path% & timeout 3
```

### Autohotkey
以下はアプリケーションキー(右側のaltやctrlのあたりにある、右クリックメニューを表示するキー)に割り当てた例。

```ahk
AppsKey::run,D:\bin1\\cl.bat
```

# Devlopment
ソースコードなど開発ネタ。

## Q: Linelevelクラスってなんですか？
Ans: cle のメソッド候補として表示させないために、クラスをはさんでいる

Utilも同様。

そういう意味では is_mac() も Util に入れるべきだが、まだできてない。

## Q: libclipboardの実装が車輪の再発明ではないですか？（libclipboard_mac.pyみたいにpyperclip使えばいいのではないですか？）
Ans: そう思います。

歴史的経緯を書いておくと、

- pyperclip 知らなかったときに自分で頑張ってクリップボード処理をつくった(windowsのみ)
- MacOS でも使いたくなったが、この処理だと動かん
- pyperclip 使うと楽そうだったので取り入れた
    - windows 側の処理はいまので動いてるしそのまま放置

## Q: 使われていない関数や名前があって汚くないですか？
Ans: 元々プライベートで使ってたものを（見せられない部分を削って）公開したものなので、粗さが目立つと思います
