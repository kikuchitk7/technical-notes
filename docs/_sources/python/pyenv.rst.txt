pyenv のインストール
===============================

前提
------------
- pyenv の `公式ページ <https://github.com/pyenv/pyenv>`_ の手順に従う。
- インストール手順の出力例は、Amazon Linux (Cloud9) でのインストール時のものである。

インストール手順
----------------------

pyenv を GitHub から clone する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ git clone https://github.com/pyenv/pyenv.git ~/.pyenv

.bash_profile に PYENV_ROOT を追記して PATH を通す
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
    $ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile

.bash_profile に pyenv init を追記する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile

shell を restart する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ exec "$SHELL"

.. note::

    - 後続手順でコマンドが通らないなどの問題が発生したら、下記のコマンドで .bash_profile を読み込む。
    
    .. code-block:: bash
    
        $ source ~/.bash_profile

pyenv に必要なパッケージのインストールを行う
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ sudo yum install gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel tk-devel libffi-devel

インストールの正常性確認を行う (バージョン確認)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ pyenv versions
    
    # 出力例
    * system (set by /home/ec2-user/.pyenv/version)


pyenv でのインストール
-----------------------------

pyenv でインストール可能なパッケージを確認する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ pyenv install --list

    # 出力例
    Available versions:
      2.1.3
      2.2.3
      2.3.7
      (以下、省略)

pyenv で特定のパッケージをインストールする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ pyenv install 3.8.1
    
    # 出力例
    pyenv: /home/ec2-user/.pyenv/versions/3.8.1 already exists
    continue with installation? (y/N) y
    Downloading Python-3.8.1.tar.xz...
    -> https://www.python.org/ftp/python/3.8.1/Python-3.8.1.tar.xz
    Installing Python-3.8.1...
    Installed Python-3.8.1 to /home/ec2-user/.pyenv/versions/3.8.1

バージョンを切り替える
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

現在のディレクトリでのみ切り替える
+++++++++++++++++++++++++++++++++++++++++++

.. code-block:: bash

    $ pyenv local 3.8.1

システム全体で切り替える
+++++++++++++++++++++++++++++++++++++++++++

.. code-block:: bash

    $ pyenv global 3.8.1

参考
-------------
- `pyenv <https://github.com/pyenv/pyenv>`_
- `pyenvのインストール、使い方、pythonのバージョン切り替えできない時の対処法 <https://qiita.com/koooooo/items/b21d87ffe2b56d0c589b>`_
