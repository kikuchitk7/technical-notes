Terraform
======================

前提
------------
- 「実践 Terraform」に従って初期設定を行う。
- Cloud9 上で Terraform を利用する。

    .. code-block:: bash
        
        $ aws --version
        aws-cli/1.16.309 Python/2.7.16 Linux/4.14.154-99.181.amzn1.x86_64 botocore/1.13.45

- IAM ユーザで Terraform を利用する。
- `tfenv <https://github.com/tfutils/tfenv>`_ を利用する。

セットアップ
------------------

IAM ユーザの作成
^^^^^^^^^^^^^^^^^^^^^^^^^^^
- (必須) プログラムによるアクセス

    - Terraform からの環境構築に必須であるため。

- (任意) マネジメントコンソールへのログイン

    - ログインしない場合は不要であるため。

- (必須) Administrator Access をアタッチする。

    - インフラ構築は管理者権限に近い権限が必要であるため。

環境変数の設定
^^^^^^^^^^^^^^^^^^^^^^
- クレデンシャルとデフォルトリージョンを設定する。

    .. code-block:: bash

    export AWS_ACCESS_KEY_ID=(Access Key ID を入力)
    export AWS_SECRET_ACCESS_KEY=(Secret Access Key を入力)
    export AWS_DEFAULT_REGION=ap-northeast-1

    echo $AWS_ACCESS_KEY_ID $AWS_SECRET_ACCESS_KEY $AWS_DEFAULT_REGION

