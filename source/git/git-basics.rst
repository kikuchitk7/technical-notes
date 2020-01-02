Gitの基本事項
==================

初期設定
---------------

ユーザ名、メールアドレスの設定
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git config --global user.name "(ユーザ名を入力)"
    $ git config --global user.email (メールアドレスを入力)

.. warning::

    - ユーザ名には "" が必要だが、メールアドレスには不要。

エディタの設定
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    # VS Code を設定する場合
    $ git config --global core.editor 'code --wait'

    # Vim を設定する場合
    $ git config --global core.editor vim

    # Vim を設定して、文字コードを UTF-8 に設定する場合
    $ git config --global core.editor 'vim -c "set fenc=utf-8"'

.. note::

    - 設定は ~/.gitconfig に書き込まれる。
    - 設定は上記を viewer で確認するか、下記のコマンドで確認できる。

    .. code-block:: bash

        $ git config --list

        # 出力例
        user.name=(ユーザ名を入力)
        user.email=(メールアドレスを入力)
        core.editor=code --wait

基本動作
-----------

ステータス確認
^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git status

変更内容の確認 (work tree vs. stage)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- work tree と stage の変更内容 (差分) を確認する。

.. code-block:: bash

    $ git diff

    # 出力例
    diff --git a/test.txt b/test.txt
    index 259b241..2691857 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -1,2 +1 @@
    -test
    -test1
    +testfile

.. note::

    - git diff コマンドは git add する前の確認コマンド。

変更のステージ追加
^^^^^^^^^^^^^^^^^^^^^^
カレントディレクトリ以下の全ての変更を追加する。

.. code-block:: bash

    $ git add .

全ての変更を追加する。

.. code-block:: bash

    $ git add -A

変更内容の確認 (stage vs. local repository)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- stage と local repository の変更内容 (差分) を確認する。

.. code-block:: bash

    $ git diff --staged

    # 出力例
    diff --git a/test.txt b/test.txt
    index 259b241..2691857 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -1,2 +1 @@
    -test
    -test1
    +testfile

.. note::

    - git diff --staged コマンドは git add した後の確認コマンド。
    - stage と local repository の差分を確認するコマンド。

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

コミットメッセージの修正
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
.. code-block:: bash

    $ git commit --amend -m 'messages'

.. note::

    -m を指定しない場合は vi の画面に切り替わって修正版のコミットメッセージを入力する。

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