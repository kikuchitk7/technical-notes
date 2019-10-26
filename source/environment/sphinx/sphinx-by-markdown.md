# Sphinx のドキュメントを Markdown で作成する

下記の手順でインストールする。

## recommonmark をインストールする
pip で 'recommonmark' をインストールする。

    pip install --upgrade recommonmark

## conf.py を編集する。
extentions に 'recommonmark' を追加する。

    extensions = ['recommonmark']

## 参考
- [Markdown](https://www.sphinx-doc.org/ja/master/usage/markdown.html)