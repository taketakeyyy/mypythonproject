# このディレクトリについて

このディレクトリは、プログラムのソースコードディレクトリです。

プログラムのソースコードは必ずこの (mypythonproject) 配下の 1 つのディレクトリ内に集約させます。（単体テストのソースコードは除く）。なぜならディレクトリとは Python のパッケージを構成するものなので、複数のディレクトリでソースコードを管理すると複数の Python パッケージを開発していることになるからです。しかし通常 1 つのプロジェクト内に複数のパッケージを含めて開発することはしません。


## main.pyはつくらない
`main.py`
```python
# coding: utf-8
import mypythonproject

def main():
    """mypythonproject パッケージを使った処理"""
    mypythonproject.do_something()

if __name__ == "__main__":
    main()
```

ライブラリとアプリケーションの区別がつかなくなるので`main.py`は作ってはダメです。

* ライブラリ： importして使うプログラム
* アプリケーション： 直接実行するプログラム

`main.py`のようなものが作成したいなら、代わりに`__main__.py`を作成します。

そして、プロジェクトホームで以下のコマンドを実行することで、`mypythonproject/__main__.py`が実行されるようになります。

```sh
$ python -m mypythonproject
```

決して、次のように実行してはいけません。

```sh
$ python -m mypythonproject/__main__.py
```

たとえ同じように動作しても、基本的に挙動は異なるので、いつもうまくいくとは限りません。


## モジュールとは

モジュールとは関数やクラスなどを別ファイルで利用できる状態で整理したソースコードのことです。モジュールはimportして使います。

### モジュールの書き方

* 普通に`.py`ファイルを作成して、そこにコードを書く
* シバン(shebang)はモジュールには不要（書いてもよいが、書く意味がない）

フィボナッチ数列をモジュール化したものは以下のようになります。

`fib.py`
```python
# -*- coding: utf-8 -*-
def fib(n):
    """n以下のフィボナッチ数列を列挙する"""
    a, b = 0, 1
    while a < n:
        print(a, end=" ")
        a, b = b, a+b
    print()

```


これを使うときは、importして使います。

`hoge.py`
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-


import fib          # fib.py を取り込む


def main():
    fib.fib(10)     # fib.py 内の fib() を呼び出す


if __name__ == '__main__':
    main()
```


モジュールのコードは必ずしも関数内に含める必要はなく、ファイル内に直接処理を書いてもOKです。

`fib.py`
```python
# -*- coding: utf-8 -*-


a, b = 0, 1

while a < n:
    print(a, end=' ')
    a, b = b, a+b

print()
```

ただしこのモジュールを`import fib`すると、importした時点でフィボナッチ数列を出力する処理が実行されます。なので基本的には関数やクラスにまとめます。


## `__name__`について

`__name__`は隠し変数で、モジュール名を表す文字列が入っています。例えば`foo.py`というモジュールであれば`__name__`は`foo`になります。

```python
import foo

print(foo.__name__)
# foo
```

しかしfoo.pyを直接実行した場合は、`__name__`は`__main__`になります。

`foo.py`
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

print(__name__)
```

```sh
$ python foo.py
__main__
```

これを利用して、`__name__`の値を判定することで、`foo.py`がモジュールとして使われているのか、直接実行されているのかを判断できるようになります。

`fib.py`
```python
# -*- coding: utf-8 -*-
def fib(n):
    """n以下のフィボナッチ数列を列挙する"""
    a, b = 0, 1
    while a < n:
        print(a, end=" ")
        a, b = b, a+b
    print()



def __sample():
    """fib()のサンプル実行"""
    fib(10)


if __name__ == "__main__":
    __sample()
```

上の構文により、1つのファイルでモジュールと実行スクリプトの両方を実装することができるようになります。


## パッケージについて

ファイルがモジュール、ディレクトリがパッケージと理解して概ね問題ありません。

ただしパッケージの中には`__init__.py`を作らなければなりません。

```python
example
├── __init__.py
├── a.py
│  └── def fib(n)
└── b.py
```

`__init__.py`は空ファイルでも問題ありません。必ず作るようにしましょう。

パッケージもモジュール同様、importして使います。

```python
import example.a

def main():
    example.a.fib(10)


if __name__ == "__main__":
    main()
```


## モジュール・パッケージの命名規則

* 全て小文字
* パッケージ名に区切り文字は禁止
* モジュール名の区切り文字は _ を使用

-(ハイフン)は使ってはいけません。importするときに減算演算子(-)とみなされエラーが発生します。

モジュール名であっても区切り文字はできるだけ使用しない方が好まれます。

パッケージ名はたとえ複数語であっても区切り文字は使用しません。


## `__init__.py`について
ディレクトリをモジュールにする化するという機能のほかに、モジュールを読み込むときの初期化として使うことができます。


## `__version__.py`について
バージョン管理をする場合、ここにバージョンを書く方法がスタンダードになりつつあります。

`__version__.py`
```python
VERSION = (5, 2, 0)

__version__ = '.'.join(map(str, VERSION))
```

`__version__.py`の議論は以下のサイトが詳しいです。

* [\_\_version\_\_ ってアンチパターンじゃね？ - Qiita](https://qiita.com/tonluqclml/items/6da162d8af1534bf5e6e)
* [バージョン番号を２度書かないために - methaneのブログ](https://methane.hatenablog.jp/entry/20120401/1333260681)


## `__all__`という名前のリストについて
[6.4.1. パッケージから * を import する - Python](https://docs.python.org/ja/3/tutorial/modules.html#importing-from-a-package)


# 参考
* [プロジェクト構成 - ゼロから学ぶ Python](https://rinatz.github.io/python-book/ch04-06-project-structures/)
