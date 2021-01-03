Continuous Apply
============================
- GitHub と AWS CodeBuild による Terraform の Continuous Apply を実現する方法をまとめる。

前提
-------------------

環境
^^^^^^^^^^^^^^^
- GitHub

    - リポジトリ: `terraform-learning-ca <https://github.com/kikuchitk7/terraform-learning-ca>`
    - ブランチ戦略: GitHub Flow

- AWS CodeBuild

    - コンテナイメージ: "hashicorp/terraform:0.12.24"

- Terraform (ローカル): 0.12.5

実現すること
^^^^^^^^^^^^^^^^^^^^
- GitHub でのイベントをトリガにして、自動で apply もしくは plan を実行する。
- ここで捕捉するイベントは下記。

    - リモート (GitHub) の master ブランチへのマージ (push)
    - Pull request が作成 (create) された場合
    - Pull request が更新 (update) された場合
    - Pull request が再オープン (reopen) された場合

自動 apply をする場合
+++++++++++++++++++++++++++++++
- リモート (GitHub) の master ブランチへのマージ (push) が発生した場合

自動 plan をする場合
+++++++++++++++++++++++++++++
- 自動 apply の場合以外のイベントが発生した場合


想定するワークフロー (GitHub Flow)
-------------------------------------------
#. 作業者が feature ブランチを作成する。
#. 作業者がローカル環境で Terraform のテンプレートを修正する。
#. 作業者が feature ブランチでの変更内容を リモート (GitHub) に push する。
#. 作業者が GitHub で Pull request を作成する。
#. 上記のイベントをトリガに CodeBuild が自動 plan を実行する。
#. レビュアが Pull request の内容を確認する。
#. レビュアが Pull request の内容を承認し、master ブランチに変更をマージする。
#. 上記のイベントをトリガに CodeBuild が自動 apply を実行する。

.. warning:: 
    - | Terraform で手動管理している環境がある場合は、CodeBuild の Build 用コンテナとのバージョン際に注意すること。
      | Build 用コンテナのバージョンを既存環境のバージョン以上にしないと下記のエラーが発生する。(Build 用コンテナ：v0.12.5, 既存環境：v0.12.20 で実行した場合)
        
        Error loading state: state snapshot was created by Terraform v0.12.20, which is newer than current v0.12.5; upgrade to Terraform v0.12.20 or greater to work with this state
    
    - Build 用コンテナイメージは codebuild_project.tf の下記の定義で設定している。

        .. code-block:: bash

            environment {
                type            = "LINUX_CONTAINER"
                compute_type    = "BUILD_GENERAL1_SMALL"
                image           = "hashicorp/terraform:0.12.24"
                privileged_mode = false
            }

設定
----------------
.. note::
    ソースコードは、GitHub の "`terraform-learning-ca <https://github.com/kikuchitk7/terraform-learning-ca>`_" を参照すること。

CodeBuild に付与する IAM role の作成
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- 権限不足による失敗を防ぐため、AdministratorAccess を付与する。

.. warning::
    - 実運用では適切な権限を付与すること。


SSM パラメータの設定 (GitHub token)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- CodeBuild と GitHub が連携できるように GitHub token を使用する。
- GitHub token は SSM パラメータストアでセキュアに保管する。
- SSM パラメータストアへの登録は AWS CLI にて手動で行う。

    .. code-block:: bash

        aws ssm put-parameter \
        --type SecureString \
        --name /continuous_apply/github_token \
        --value (GitHub token を記入)


CodeBuild のプロジェクトの作成
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- CodeBuild ブロジェクトでは、下記を設定する。

    - source: GitHub
    - artifacts: 今回はなし
    - environment: Build 用コンテナ

        - image で利用するコンテナイメージを設定する。
        - HashiCorp の公式イメージを利用する。
        - `DockerHub <https://hub.docker.com/r/hashicorp/terraform/tags>`_ で利用可能バージョンを確認すること。

    - provisoner: Terraform の機能で GitHub token が取得できないので、"local-exec" を利用

        .. code-block:: bash

            provisioner "local-exec" {
                command = <<-EOT
                aws codebuild import-source-credentials \
                    --server-type GITHUB \
                    --auth-type PERSONAL_ACCESS_TOKEN \
                    --token $GITHUB_TOKEN
                EOT

                environment = {
                GITHUB_TOKEN = data.aws_ssm_parameter.github_token.value
                }
            }

CodeBuild の Webhook の作成
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- GitHub からの Webhook を受け取るための定義を追加する。
- GitHub 側は自動で設定されるため、対処不要。
- ここで補足するイベントは下記。
    
    - リモート (GitHub) の master ブランチへのマージ (push)
    - Pull request が作成 (create) された場合
    - Pull request が更新 (update) された場合
    - Pull request が再オープン (reopen) された場合


CodeBuild のビルドの設定
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


buildspec.yml
++++++++++++++++++++++++++
- CodeBuild のビルド処理を定義する。
- 今回は環境変数 (env) の設定とビルドフェーズ (phase) の設定のみを定義する。


scripts/build.sh
++++++++++++++++++++++++++
- 補足したイベントで処理を分ける。

    - リモート (GitHub) の master ブランチへのマージ (push) の場合： apply.sh の実行
    - 上記の場合以外： plan.sh の実行


scripts/apply.sh
++++++++++++++++++++++++++
- "terraform init" と "terraform apply" を実行する。

.. note::
    - "-input=false" は入力を求めるプロンプトを表示させないためのオプション


scripts/plan.sh
++++++++++++++++++++++++++
- "terraform init" と "terraform plan" を実行する。


参考
--------------
- 「実践 Terraform」
- 「`hashicorp/terraform (DockerHub) <https://hub.docker.com/r/hashicorp/terraform/tags>`_」
- 「`「一回くらいローカルでterraform applyしてもいいだろう。その時はそう思ってました。」 tfstateをダウングレードしなくてはいけないときの指南書 <https://note.com/_k_e_k_e/n/ne74b973c0b61>`_」
