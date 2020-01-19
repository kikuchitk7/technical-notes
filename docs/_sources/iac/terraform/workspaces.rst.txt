Workspaces
======================

概要
------------
- | Terraform の構成は、バックエンドと紐づけられている。
  | そのバックエンドには、操作の実行方法とTerraform状態などの永続データが保管されている場所が定義されている。

    Each Terraform configuration has an associated backend that defines how operations are executed and where persistent data such as the Terraform state are stored.

- | バックエンドの永続データは何らかの workspace に属する。
  | 初期状態では "default" という workspace しかないので、Terraform は1つの状態しか持てない。

    The persistent data stored in the backend belongs to a workspace. 
    Initially the backend has only one workspace, called "default", and thus there is only one Terraform state associated with that configuration.

- workspace を意識していなくても "default" の workspace が存在し、ここで作業をしている。

    Terraform starts with a single workspace named "default".
    This workspace is special both because it is the default and also because it cannot ever be deleted.
    If you've never explicitly used workspaces, then you've only ever worked on the "default" workspace.

- workspace を分けるということは、すなわち、バックエンドを分けるということ。

    - workspaces は Terraform の機能としてバックエンドの分割を実現してくれる。
    - | Git の各ブランチでバックエンドの設定を分ければ同様のことが実現できる。
      | 下記は AWS でバックエンドに S3 を使う場合。環境ごとにこの定義を変えれば実現できる。

            .. code-block:: bash

                terraform {
                    backend "s3" {
                        bucket = "<bucket>"
                        key = "<directory>/terraform.tfstate"
                        region = "ap-northeast-1"
                    }
                }

- tfstate ファイルをローカル管理している状態で workspace を作成すると、"terraform.tfstate.d" というディレクトリに workspace ごとに tfstate ファイルが保管されることになる。

    For local state, Terraform stores the workspace states in a directory called terraform.tfstate.d.

- 環境差分は下記の方法で表現できる。

    - ${terraform.workspace} で workspace の名称を参照する。

        .. code-block:: bash

            resource "aws_instance" "example" {
                tags = {
                    Name = "web - ${terraform.workspace}"
                }

                # ... other arguments
            }

    - workspace ごとに tfvars を作成する。
    - "count" や三項演算子を駆使する。

        .. code-block:: bash

            resource "aws_instance" "example" {
                count = "${terraform.workspace == "default" ? 5 : 1}"

                # ... other arguments
            }
    
        .. note::

            Terraform の文法には条件分岐がないので、workspace 名称等による条件分岐が不可能。

ユースケース
-----------------

前提
^^^^^^^^^^^
- master ブランチと default workspace (本番環境) が対応している。
- | パブリックリソースの設定などを除いて default workspace と他の workspace にリソースに差分がない。
  | すなわち、特定の workspace にしか存在しないリソースの作成は認めない運用になっている。

考えられるユースケース
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- | feature ブランチの作成 (機能追加やバグ修正) のタイミングでこれに対応する workspace を作成する。
  | 本番環境と同一にできないリソースは環境差分として定義する。
- リソースの追加と動作確認を実施して問題がないことを確かめた上で、master ブランチへのマージ、本番環境へのデプロイを行う。
- デプロイ後に workspace を削除する。(永続させる workspace は default のみ)

    Non-default workspaces are often related to feature branches in version control.
    The default workspace might correspond to the "master" or "trunk" branch, which describes the intended state of production infrastructure.
    When a feature branch is created to develop a change, the developer of that feature might create a corresponding workspace and deploy into it a temporary "copy" of the main infrastructure so that changes can be tested without affecting the production infrastructure.
    Once the change is merged and deployed to the default workspace, the test infrastructure can be destroyed and the temporary workspace deleted.

.. note::

    - 公式ガイドで謳われているユースケースを読むと GitHub Flow での運用が一番馴染みそう。

Pros and Cons
---------------------

Pros
^^^^^^^^^^^^
- 重複が許されないバブリックなリソースやリソースの名前などの環境差分を除いて、単一の tf ファイル群での運用が可能となる。
- 本番環境と同一構成の環境を簡単に複製できる。

Cons
^^^^^^^^^^^^
- | Workspaces を使うと、workspace : tf ファイルが、多 : 1 の関係になる。
  | 1つの tf ファイルを共有することになるので、特定の workspace のみに存在するリソースの定義が難しい。
  | "count" や三項演算子を駆使すれば実現できるが、テンプレートが複雑になるので、維持運用が困難になる可能性がある。
- | default 以外の workspace を一時的な環境とする場合、環境差分を毎回定義しなければいけなくなる。
  | 環境差分をリポジトリ内に持つとなると、本番稼働で不要なファイルが存在することになる。
- | Workspaces の利用有無に関わらず、環境差分となり得る定義を切り出して管理する必要がある。
  | 特定の環境に固有のリソースの作成を認めないルールとするなら利用価値があるが、そうでなければ制約が強くなるだけなのであえて利用するメリットがない。

参考
----------
- `Workspaces <https://www.terraform.io/docs/state/workspaces.html>`_
- `Terraform Workspacesの基礎と使い方について考えてみた！ #AdventCalendar <https://dev.classmethod.jp/cloud/how-to-use-terraform-workspace/>`_
