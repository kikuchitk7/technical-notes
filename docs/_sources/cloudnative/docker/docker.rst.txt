Docker
====================

イメージに関する操作
---------------------------


コンテナに関する操作
---------------------------

docker container run
^^^^^^^^^^^^^^^^^^^^^^^^^^^

オプション
++++++++++++++++++

    - -t : 擬似ターミナル生成
    - -i : 標準入力を有効

.. note:: 
    オプションの違いによる挙動の違いを示す。

    - "-t" のみ : 入力を一切受け付けない

    .. code-block:: bash
        $ docker container run -t centos:latest

        # 擬似ターミナルが表示されるが、操作ができない。
        # exit も Command + c も効かないので、kill して終了した。
        [root@2386f050d3f8 /]# 

        exit

        ^C^C
        [root@2386f050d3f8 /]# 

    - "-i" のみ : 擬似ターミナルなしで入力が可能

    .. code-block:: bash

        $ docker container run -i centos:latest

        # 擬似ターミナルは表示されていないが、操作は可能
        date
        Sat Mar  7 01:17:31 UTC 2020

        exit
        $

    - "-it" : 擬似ターミナルありで入力が可能 (通常はこのパターンで利用)

    .. code-block:: bash

        $ docker container run -it centos:latest

        # 通常のサーバ操作のように操作が可能
        [root@e65c61a608e5 /]# 
        [root@e65c61a608e5 /]# date
        Sat Mar  7 01:17:10 UTC 2020
        [root@e65c61a608e5 /]# 
        [root@e65c61a608e5 /]# exit
        exit
        $ 


docker container exec
^^^^^^^^^^^^^^^^^^^^^^^^^^^
実行中のコンテナで任意のコマンドを実行できる。

.. code-block:: bash

    # testcontainer (CentOS) を起動
    $ docker container run -t -d --rm --name testcontainer centos:latest
    0f49d3097f5dfb365f55d8d88b32e0284bee588edd14394a2b4dbb93d5c35420

    # 起動確認
    $ docker container ls
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    0f49d3097f5d        centos:latest       "/bin/bash"         7 seconds ago       Up 6 seconds                            testcontainer

    # date コマンドを実行させる
    $ docker container exec testcontainer date
    Sat Mar  7 02:02:05 UTC 2020
