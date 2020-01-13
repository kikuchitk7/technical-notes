Terraform のセットアップ
================================

前提
------------
- 「実践 Terraform」に従って初期設定を行う。
- Cloud9 上で Terraform を利用する。

    .. code-block:: bash

        $ aws --version
        aws-cli/1.16.309 Python/2.7.16 Linux/4.14.154-99.181.amzn1.x86_64 botocore/1.13.45

- IAM ユーザで Terraform を利用する。
- Terraform のバージョンアップに柔軟に対応するため、`tfenv <https://github.com/tfutils/tfenv>`_ を利用する。
- Terraform は 2020年1月13日現在の最新バージョンである v0.12.19 を利用する。

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

        $ export AWS_ACCESS_KEY_ID=(Access Key ID を入力)
        $ export AWS_SECRET_ACCESS_KEY=(Secret Access Key を入力)
        $ export AWS_DEFAULT_REGION=ap-northeast-1

- 設定の正常性を確認する。

    .. code-block:: bash

        $ echo $AWS_ACCESS_KEY_ID $AWS_SECRET_ACCESS_KEY $AWS_DEFAULT_REGION

tfenv のマニュアルインストール
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- tfenv を GitHub のリポジトリからクローンする。

    .. code-block:: bash

        $ git clone https://github.com/tfutils/tfenv.git ~/.tfenv

        # 出力例
        Cloning into '/home/ec2-user/.tfenv'...
        remote: Enumerating objects: 916, done.
        remote: Total 916 (delta 0), reused 0 (delta 0), pack-reused 916
        Receiving objects: 100% (916/916), 171.51 KiB | 474.00 KiB/s, done.
        Resolving deltas: 100% (573/573), done.

- パスを通す。

    .. code-block:: bash

        $ echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bash_profile

- .bash_profile を読み込んで反映する。

    .. code-block:: bash

        $ source ~/.bash_profile


- インストール後の正常性を確認する。

    .. code-block:: bash

        $ tfenv --version

        # 出力例
        tfenv 2.0.0-alpha3-1-ge21f9e0

tfenv による Terraform のインストール
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- 最新バージョンの Terraform をインストールする。2020年1月2日時点では v0.12.18 が最新。

    .. code-block:: bash

        $ tfenv install latest

        # 出力例
        Installing Terraform v0.12.18
        Downloading release tarball from https://releases.hashicorp.com/terraform/0.12.18/terraform_0.12.18_linux_amd64.zip
        ################################################################################################################################## 100.0%
        Downloading SHA hash file from https://releases.hashicorp.com/terraform/0.12.18/terraform_0.12.18_SHA256SUMS
        No keybase install found, skipping OpenPGP signature verification
        Archive:  tfenv_download.ztm7xc/terraform_0.12.18_linux_amd64.zip
          inflating: /home/ec2-user/.tfenv/versions/0.12.18/terraform  
        Installation of terraform v0.12.18 successful
        Switching to v0.12.18
        Switching completed

    .. note::

        - 特定バージョンをインストールしたい場合は remote-list でインストール可能なバージョンを調べてインストールする。

        .. code-block:: bash

            $ tfenv list-remote

            # 出力例
            0.12.18
            0.12.17
            0.12.16
            (以下、省略)

tfenv で特定バージョンをインストールした場合の挙動
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- 自動でインストールしたバージョンに切り替わるので注意する。インストールメッセージの最後にも "Switching completed" と出力されている。

    .. code-block:: bash

        $ tfenv install 0.12.19
        Installing Terraform v0.12.19
        Downloading release tarball from https://releases.hashicorp.com/terraform/0.12.19/terraform_0.12.19_linux_amd64.zip
        ############################################################################################################################################### 100.0%
        Downloading SHA hash file from https://releases.hashicorp.com/terraform/0.12.19/terraform_0.12.19_SHA256SUMS
        No keybase install found, skipping OpenPGP signature verification
        Archive:  tfenv_download.hHWSwD/terraform_0.12.19_linux_amd64.zip
          inflating: /home/ec2-user/.tfenv/versions/0.12.19/terraform  
        Installation of terraform v0.12.19 successful
        Switching to v0.12.19
        Switching completed

tfenv でインストールされているバージョンの確認
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- tfenv でインストールされているバージョンは下記のコマンドで確認する。

    .. code-block:: bash

        $ tfenv list
        * 0.12.19 (set by /home/ec2-user/environment/terraform-learning/.terraform-version)
          0.12.18

tfenv で利用するバージョンの切替
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- tfenv で利用するバージョンは下記のコマンドで切り替える。

    .. code-block:: bash

    $ tfenv use 0.12.18
    Switching to v0.12.18
    Switching completed

- list コマンドでも切り替わりを確認できる。

    .. code-block:: bash

    $ tfenv list
      0.12.19
    * 0.12.18 (set by /home/ec2-user/environment/terraform-learning/.terraform-version)

参考文献
----------------
- `tfutils/tfenv <https://github.com/tfutils/tfenv>`_
