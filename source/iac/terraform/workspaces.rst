Workspaces
======================

概要
------------
- Workspace を分けることができる。
- Workspace を意識していなくても "default" の Workspace が存在し、ここで作業をしている。
- Workspace を分けるということは、すなわち、tfstate ファイルを分けるということ。
- tfstate ファイルをローカル管理している状態で Workspace を作成すると、"terraform.tfstate.d" というディレクトリに Workspace ごとに tfstate ファイルが保管されることになる。
- Workspace : tf ファイルが、多 : 1 の関係になる。
- ${terraform.workspace} や Workspace ごとに tfvars を作成することで環境差分を持たせられる。
- 1つの tf ファイルを共有することになるので、特定の Workspace のみに存在するリソースの定義が難しい。
- Terraform の文法には条件分岐がないが、"count" や三項演算子を駆使すると実現はできる。テンプレートが複雑になるので、維持運用が困難になる可能性がある。

参考
----------
- `Workspaces <https://www.terraform.io/docs/state/workspaces.html>`_
- `Terraform Workspacesの基礎と使い方について考えてみた！ #AdventCalendar <https://dev.classmethod.jp/cloud/how-to-use-terraform-workspace/>`_
