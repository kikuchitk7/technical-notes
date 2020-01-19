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


参考
-------------

