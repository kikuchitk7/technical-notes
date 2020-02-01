Sphinx でカスタム CSS を適用する
========================================

概要
----------
- 下記を対応するためにカスタム CSS を設定する。

.. 

    #. 見出しにアンダーラインをつける。
    #. ドキュメントの領域を画面幅に合わせる。
    #. テーブルを画面幅に合わせる。

設定
------------

_static/css/ にカスタム CSS を作成する。
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: css

    @import url("theme.css");
    
    .wy-nav-content {
        max-width: none;
    }

    h1,h2,h3,h4,h5,h6 {
        border-bottom: 1px solid #ccc;
    }

    .wy-table-responsive table td, .wy-table-responsive table th {
        white-space: normal;
    }

    colgroup {
        display: none;
    }

copy.py に下記を追記する。

.. code-block:: python

    html_style = "css/my_theme.css"


参考
----------
- `Sphinx の sphinx_rtd_theme をカスタマイズする <http://kuttsun.blogspot.com/2016/11/sphinx-sphinxrtdtheme.html>`_
