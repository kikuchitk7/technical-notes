Gitの基本事項
==================

基本動作
-----------

ステータス確認
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git status

変更のステージ追加
^^^^^^^^^^^^^^^^^^^^^^
カレントディレクトリ以下の全ての変更を追加する。

.. code-block:: bash

    $ git add .

全ての変更を追加する。

.. code-block:: bash

    $ git add -A

変更のコミット
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git commit -m 'messages'

変更のプッシュ
^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git push origin master

.. note::

    - origin：リモートリポジトリの別名
    - master：master ブランチ


操作履歴の確認
------------------

コミットログの確認
^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git log

ブランチ操作
----------------

現在のブランチの確認
^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git branch

現在のブランチに * がつく。

::

    * dev
    master

ブランチの切替
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git checkout master

::

    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.

ブランチの作成＋切替
^^^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git checkout -b dev

::

    Switched to a new branch 'dev'


ブランチの削除
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git branch -D dev

::

    Deleted branch dev (was cf70845).