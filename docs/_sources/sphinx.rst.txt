Sphinx と GitHub Pages を使ってドキュメントを公開する
=====================================================

前提
=====

- macOS Catalina バージョン 10.15
- Python 3.7.4
- Public な GitHub repository を作成済

環境構築 (ローカル)
====================

#. 公開用の repository を clone する。

.. code-block:: bash

   $ git clone git clone https://github.com/kikuchitk7/technical-notes.git

#. clone したディレクトリに移動する。

.. code-block:: bash

   $ cd ./technical-notes

#. Sphinx を pip でインストールする。

.. code-block:: bash

   $ pip install -U sphinx

::
  
  Collecting sphinx
  (中略)
  Installing collected packages: sphinxcontrib-devhelp, sphinxcontrib-applehelp, sphinxcontrib-htmlhelp, sphinxcontrib-jsmath, sphinxcontrib-qthelp, sphinxcontrib-serializinghtml, sphinx
  Successfully installed sphinx-2.2.0 sphinxcontrib-applehelp-1.0.1 sphinxcontrib-devhelp-1.0.1 sphinxcontrib-htmlhelp-1.0.2 sphinxcontrib-jsmath-1.0.1 sphinxcontrib-qthelp-1.0.2 sphinxcontrib-serializinghtml-1.1.3

#. 拡張テーマ (sphinx_rtd_theme) をインストールする。

.. code-block:: bash

   $ pip install sphinx_rtd_theme

::

  Collecting sphinx_rtd_theme
  (中略)
  Installing collected packages: sphinx-rtd-theme
  Successfully installed sphinx-rtd-theme-0.4.3

#. sphinx-quickstart でプロジェクトを作成する。

.. code-block:: bash

   $ sphinx-quickstart


::

  Sphinx 2.2.0 クイックスタートユーティリティへようこそ。
  
  以下の設定値を入力してください（Enter キーのみ押した場合、
  かっこで囲まれた値をデフォルト値として受け入れます）。
  
  選択されたルートパス: .
  
  Sphinx 出力用のビルドディレクトリを配置する方法は2つあります。
  ルートパス内にある "_build" ディレクトリを使うか、
  ルートパス内に "source" と "build" ディレクトリを分ける方法です。
  > ソースディレクトリとビルドディレクトリを分ける（y / n） [n]: y
  
  プロジェクト名は、ビルドされたドキュメントのいくつかの場所にあります。
  > プロジェクト名: docs
  > 著者名（複数可）: kikuchitk7
  > プロジェクトのリリース []: 
  
  If the documents are to be written in a language other than English,
  you can select a language here by its language code. Sphinx will then
  translate text that it generates into that language.
  
  For a list of supported codes, see
  https://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.
  > プロジェクトの言語 [en]: ja
  
  ファイル ./source/conf.py を作成しています。
  ファイル ./source/index.rst を作成しています。
  ファイル ./Makefile を作成しています。
  ファイル ./make.bat を作成しています。
  
  終了：初期ディレクトリ構造が作成されました。
  
  マスターファイル ./source/index.rst を作成して
  他のドキュメントソースファイルを作成します。次のように Makefile を使ってドキュメントを作成します。
   make builder
   "builder" はサポートされているビルダーの 1 つです。 例: html, latex, または linkcheck。

下記がインストールされる。

.. code-block:: bash
   
   $ tree .

::
  
  .
  ├── Makefile
  ├── README.md
  ├── build
  ├── make.bat
  └── source
      ├── _static
      ├── _templates
      ├── conf.py
      └── index.rst
  
  4 directories, 5 files

#. Makefile を修正する。

  - BUILDDIR を "docs" に変更する。

  ::

    BUILDDIR      = docs

  .. note::
     GitHub Pages でこの"docs" ディレクトリを公開する。

  - build のコマンドを変更する。

  ::

    #%: Makefile
    #  @$(SPHINXBUILD) -M $@ "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)
  
    html:
      @$(SPHINXBUILD) -b html "$(SOURCEDIR)" "$(BUILDDIR)" $(SPHINXOPTS) $(O)

  .. warning::
     -M オプションでは出力フォルダの指定ができそうになかった。
     スマートな方法ではないが元々の記載をコメントアウトして、 -b オプション版を追記した。

#. conf.py を修正する。

  - ファイルの頭の方に import 文を追記する。

  .. code-block:: python
     
     import sphinx_rtd_theme

  - "extentions" に拡張モジュールを追加して下記とする。

    .. code-block:: python

       extensions = [
           'sphinx_rtd_theme',
           'sphinx.ext.mathjax',
           'sphinx.ext.todo',
           'sphinx.ext.githubpages',
       ]

  - "html_theme" を 'alabaster' から 'sphinx_rtd_theme' に修正する。

  .. code-block:: python
     
     html_theme = 'sphinx_rtd_theme'

  - 上記の付近に "html_theme_path" を追加する。

  .. code-block:: python
     
     html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]

#. ビルドする。

.. code-block:: bash

   $ make html

#. docs ディレクトリに .nojekyll を作成する。

.. code-block:: bash

   $ mkdir ./docs
   $ touch ./docs/.nojekyll

#. 変更内容をステージ、コミット、プッシュする。

.. code-block:: bash

   $ git add -a
   $ git commit -m 'Initial commit'
   $ git push origin master

GitHub の設定
====================

#. GitHub Pages の公開設定をする。

  - GitHub の Settings で、 Source を "master branch / docs folder" に変更する。

  .. image:: ../images/GitHub_Pages_Settings_Source.png

GitHub Pages の確認
=======================

#. 公開されたページ(https://kikuchitk7.github.io/mynavi-blogs/)を確認する。


参考
=======================

- `Sphinxの導入とGitHubページの作成 <http://debugroom.github.io/doc/memo/work/sphinx/install.html#>`_
- `4. Sphinxでの文章の書き方(reStructuredText) <https://planset-study-sphinx.readthedocs.io/ja/latest/04.html#id14>`_
- `Sphinx 2.2.0 <https://pypi.org/project/Sphinx/>`_
- `SphinxとGitHub Pagesで技術ノートを公開しよう！ <https://qiita.com/tutuz/items/88a32d94d700b33dc3ea>`_
- `GitHub PagesでSphinxにCSSが適用されない!! <http://hirohio.hatenablog.com/entry/2018/07/17/001045>`_
- `reStructuredText入門 <https://sphinx-users.jp/articles/expertpython/restructuredtext.html>`_