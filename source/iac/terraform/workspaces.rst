Workspaces
======================

概要
------------
- | Terraform の構成は、バックエンドと紐づけられている。
  | そのバックエンドには、操作の実行方法とTerraform状態などの永続データが保管されている場所が定義されている。

    Each Terraform configuration has an associated backend that defines how operations are executed and where persistent data such as the Terraform state are stored.

- | バックエンドの永続データは何らかの Workspace に属する。
  | 初期状態では "default" という Workspace しかないので、Terraform は1つの状態しか持てない。

    The persistent data stored in the backend belongs to a workspace. 
    Initially the backend has only one workspace, called "default", and thus there is only one Terraform state associated with that configuration.

- Workspace を意識していなくても "default" の Workspace が存在し、ここで作業をしている。

    Terraform starts with a single workspace named "default".
    This workspace is special both because it is the default and also because it cannot ever be deleted.
    If you've never explicitly used workspaces, then you've only ever worked on the "default" workspace.

- Workspace を分けるということは、すなわち、バックエンドを分けるということ。

    - Workspaces は Terraform の機能としてバックエンドの分割を実現してくれる。
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

- tfstate ファイルをローカル管理している状態で Workspace を作成すると、"terraform.tfstate.d" というディレクトリに Workspace ごとに tfstate ファイルが保管されることになる。

    For local state, Terraform stores the workspace states in a directory called terraform.tfstate.d.

- 環境差分は下記の方法で表現できる。

    - ${terraform.workspace} で Workspace の名称を参照する。

        .. code-block:: bash

            resource "aws_instance" "example" {
                tags = {
                    Name = "web - ${terraform.workspace}"
                }

                # ... other arguments
            }

    - Workspace ごとに tfvars を作成する。
    - "count" や三項演算子を駆使する。

        .. code-block:: bash

            resource "aws_instance" "example" {
                count = "${terraform.workspace == "default" ? 5 : 1}"

                # ... other arguments
            }
    
        .. note::

            Terraform の文法には条件分岐がないので、Workspace 名称等による条件分岐が不可能。

Pros and Cons
---------------------

Pros
^^^^^^^^^^^^
- 重複が許されないバブリックなリソースやリソースの名前などの環境差分を除いて、単一の tf ファイルで運用が可能となる。
- 本番環境への適用前に検証用の環境を作って動作確認をし、問題がないことを確かめて本番環境にデプロイするなどの運用が実現可能となる。
- 本番環境と同一構成での再現試験なども実現可能となる。

Cons
^^^^^^^^^^^^
- | Workspace を使うと、Workspace : tf ファイルが、多 : 1 の関係になる。
  | 1つの tf ファイルを共有することになるので、特定の Workspace のみに存在するリソースの定義が難しい。
  | "count" や三項演算子を駆使すれば実現できるが、テンプレートが複雑になるので、維持運用が困難になる可能性がある。

参考
----------
- `Workspaces <https://www.terraform.io/docs/state/workspaces.html>`_
- `Terraform Workspacesの基礎と使い方について考えてみた！ #AdventCalendar <https://dev.classmethod.jp/cloud/how-to-use-terraform-workspace/>`_
