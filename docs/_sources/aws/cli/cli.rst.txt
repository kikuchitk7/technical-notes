AWS CLI
================

前提
-----------------

利用バージョン
^^^^^^^^^^^^^^^^^^^^^^
- Cloud9 の Amazon Linux におけるデフォルトバージョンを利用。

    $ aws --version
    aws-cli/1.16.309 Python/2.7.16 Linux/4.14.154-99.181.amzn1.x86_64 botocore/1.13.45

S3
-----------

S3 Bucket の一覧表示
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ aws s3 ls

    # 出力例
    2020-01-23 06:27:53 <bucket-name>
