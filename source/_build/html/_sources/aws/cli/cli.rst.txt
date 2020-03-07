AWS CLI
================

前提
-----------------

利用バージョン
^^^^^^^^^^^^^^^^^^^^^^
- Cloud9 の Amazon Linux におけるデフォルトバージョンを利用。

.. code-block:: bash

    $ aws --version
    aws-cli/1.16.309 Python/2.7.16 Linux/4.14.154-99.181.amzn1.x86_64 botocore/1.13.45


S3
-----------

情報のリスト表示 (ls)
^^^^^^^^^^^^^^^^^^^^^^^^^

S3 Bucket の一覧表示
+++++++++++++++++++++++++++++

.. code-block:: bash

    $ aws s3 ls

    # 出力例
    2020-01-23 06:27:53 <bucket-name>

S3 Bucket 内のオブジェクトの一覧表示
++++++++++++++++++++++++++++++++++++++++++++

.. code-block:: bash

    $ aws s3 ls s3://<bucket-name>/<object-name>

    # 出力例
    2020-02-16 04:35:31       8528 terraform.tfstate


Bucket ポリシーの表示
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    $ aws s3api get-bucket-policy --bucket <bucket-name>

    # 出力例
    {
        "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"arn:aws:iam::<aws-account-id>:root\"},\"Action\":\"s3:PutObject\",\"Resource\":\"arn:aws:s3:::<bucket-name>>/*\"}]}"
    }
