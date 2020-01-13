Terraform の 設定
============================

tfstate ファイルを S3 で保管する
--------------------------------------

前提
^^^^^^^^^^
- 下記の設定で tfstate 管理用の S3 Bucket が作成されている。

    - バージョニング：有効
    - デフォルト暗号化：有効 (AES-256)
    - バブリックアクセスブロック：有効

- S3 Bucket 内に tfstate 管理用のディレクトリが作成されている。
- .gitignore に tfstate の除外が定義されている。

    .. code-block:: bash

        .terraform/terraform.tfstate

    .. warning::

        - main.tf に tfstate を S3 Bucket で管理する定義を追加した後に terraform init を実行するとローカルに .terraform/terraform.tfstate を作成しようとしているように見えるので、これを除外しておく。

        .. code-block:: bash

            $ git status

            # 出力例
            # (中略)
            Untracked files:
              (use "git add <file>..." to include in what will be committed)

                    .terraform/terraform.tfstate

            nothing added to commit but untracked files present (use "git add" to track)

設定手順
^^^^^^^^^^^^
- main.tf に下記を追加する。

    .. code-block:: bash

        terraform {
            backend "s3" {
                bucket = "<bucket>"
                key = "<directory>/terraform.tfstate"
                region = "ap-northeast-1"
            }
        }

- terraform init コマンドを実行する。
- 格納先に指定した S3 Bucket を参照して、tfstate が作成されたことを確認する。 

    .. code-block:: bash

        $ aws s3 ls <bucket>/<directory>/
        
        # 出力例
        2020-01-13 01:46:06          0 
        2020-01-13 01:57:04       7240 terraform.tfstate
