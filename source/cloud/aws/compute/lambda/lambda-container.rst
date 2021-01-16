Lambda ランタイムのコンテナイメージサポート
=============================================

概要
-----------
* 従来から AWS Lambda では、関数コードや依存関係を ZIP 形式でパッケージ化してデプロイをする必要があった。これはデプロイパッケージと呼ばれ、AWS re:Invent 2020 のアップデートで、Docker コンテナイメージによるパッケージ化がサポートされた。
* Docker コンテナイメージによるデプロイパッケージは、例えば下記の点で優位性がある。

    * デプロイパッケージのサイズ制限が緩和される。 (ZIP: 250 MB → Docker コンテナイメージ：10GB)
    * ECS/Fargate などによる他のコンテナベースのアプリとデプロイ方法の統一が可能である。

* AWS から Lambda 関数向けの Docker コンテナイメージ (AWS ベースイメージ) 及びその基になる Dockerfile が OSS として提供されている。要件に合わせて関数コードや依存関係を追加して利用することが可能である。
* AWS ベースイメージは AWS により管理され、定期的に更新される。
* AWS ベースイメージで提供されないランタイム (言語・バージョン) を利用したい場合は、Amazon Linux 2 のベースイメージを利用して、カスタムランタイムを構築することが可能である。Alpine、CentOS、Debian、Ubuntu も選択可能。Amazon Linux は後述の RIC がサポート対象外のため利用不可。
* 関数コードから Lambda を操作するには Runtime API が必要。Runtime API は Lambda からの invocation event の受信や Lambda への関数の処理結果の送信を HTTP で行う。
* Docker コンテナイメージの構築を容易にするために Runtime API をパッケージ化して、Runtime interface client (RIC) としてリリースされた。
* AWS ベースイメージには RIC が組み込まれているが、カスタムランタイムには組み込まれていないので自分でインストールする必要がある。
* Extensions API (Lambda Extensions) を利用して、サードパーティのモニタリングツールを組み込むなどのカスタマイズも可能である。


詳細
-------------

AWS ベースイメージ
^^^^^^^^^^^^^^^^^^^^^^^^^^^
* AWS ベースイメージとして、下記の表に示すコンテナイメージが提供されている。
* AWS ベースイメージは、Docker Hub と Amazon ECR Public (`Amazon ECR Public Gallery <https://gallery.ecr.aws/>`_) で公開されている。
* それぞれの AWS ベースイメージの Dockerfile は、GitHub (`aws/aws-lambda-base-images <https://github.com/aws/aws-lambda-base-images>`_ で公開されている。

.. list-table::
    :header-rows: 1

    * - Tags
      - Runtime
      - OS
      - Dockerfile
    * - 12
      - nodejs12.x
      - Amazon Linux 2
      - `Dockerfile.nodejs12.x <https://github.com/aws/aws-lambda-base-images/blob/nodejs12.x/Dockerfile.nodejs12.x>`_
    * - 10
      - nodejs10.x
      - Amazon Linux 2
      - `Dockerfile.nodejs10.x <https://github.com/aws/aws-lambda-base-images/blob/nodejs10.x/Dockerfile.nodejs10.x>`_
    * - 3, 3.8
      - python3.8
      - Amazon Linux 2
      - `Dockerfile.python3.8 <https://github.com/aws/aws-lambda-base-images/blob/python3.8/Dockerfile.python3.8>`_
    * - 3.7
      - python3.7
      - Amazon Linux 2
      - `Dockerfile.python3.7 <https://github.com/aws/aws-lambda-base-images/blob/python3.7/Dockerfile.python3.7>`_
    * - 3.6
      - python3.6
      - Amazon Linux 2018.03
      - `Dockerfile.python3.6 <https://github.com/aws/aws-lambda-base-images/blob/python3.6/Dockerfile.python3.6>`_
    * - 2, 2.7
      - python2.7
      - Amazon Linux 2018.03
      - `Dockerfile.python2.7 <https://github.com/aws/aws-lambda-base-images/blob/python2.7/Dockerfile.python2.7>`_
    * - 11
      - Java 11 (Corretto)
      - Amazon Linux 2
      - `Dockerfile.java11 <https://github.com/aws/aws-lambda-base-images/blob/java11/Dockerfile.java11>`_
    * - 11
      - Java 11 (Corretto)
      - Amazon Linux 2
      - `Dockerfile.java11 <https://github.com/aws/aws-lambda-base-images/blob/java11/Dockerfile.java11>`_
    * - 8.al2
      - Java 8 (Corretto)
      - Amazon Linux 2
      - `Dockerfile.java8.al2 <https://github.com/aws/aws-lambda-base-images/blob/java8.al2/Dockerfile.java8.al2>`_
    * - 8
      - Java 8 (OpenJDK)
      - Amazon Linux 2018.03
      - `Dockerfile.java8 <https://github.com/aws/aws-lambda-base-images/blob/java8/Dockerfile.java8>`_
    * - 5.0
      - .NET 5.0
      - Amazon Linux 2
      - `Dockerfile <https://github.com/aws/aws-lambda-dotnet/blob/master/LambdaRuntimeDockerfiles/dotnet5/Dockerfile>`_
    * - core3.1
      - .NET Core 3.1
      - Amazon Linux 2
      - `Dockerfile.dotnetcore3.1 <https://github.com/aws/aws-lambda-base-images/blob/dotnetcore3.1/Dockerfile.dotnetcore3.1>`_
    * - core2.1
      - .NET Core 2.1
      - Amazon Linux 2018.03
      - `Dockerfile.dotnetcore2.1 <https://github.com/aws/aws-lambda-base-images/blob/dotnetcore2.1/Dockerfile.dotnetcore2.1>`_
    * - 1
      - Go 1.x
      - Amazon Linux 2018.03
      - `Dockerfile.go1.x <https://github.com/aws/aws-lambda-base-images/blob/go1.x/Dockerfile.go1.x>`_
    * - 2, 2.7
      - Ruby 2.7
      - Amazon Linux 2
      - `Dockerfile.ruby2.7 <https://github.com/aws/aws-lambda-base-images/blob/ruby2.7/Dockerfile.ruby2.7>`_
    * - 2.5
      - Ruby 2.5
      - Amazon Linux 2018.03
      - `Dockerfile.ruby2.5 <https://github.com/aws/aws-lambda-base-images/blob/ruby2.5/Dockerfile.ruby2.5>`_

.. warning:: 

    Amazon Linux 2018.03 は、Amazon Linux の最新のリリース。Amazon Linux は、2020年12月31日で EOL となるため、商用では利用しないことが望ましい。

* Amazon Linux 2 のベースイメージを利用して、カスタムランタイムを構成することが可能である。
* Alpine、CentOS、Debian、Ubuntu も選択可能。Amazon Linux は後述の RIC がサポート対象外のため利用不可。

.. list-table::
    :header-rows: 1

    * - Tags
      - Runtime
      - OS
    * - al2
      - provided.al2
      - Amazon Linux 2
    * - alami
      - provided
      - Amazon Linux


Runtime interface client (RIC)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
**(再掲)**

* 関数コードから Lambda を操作するには Runtime API が必要。Runtime API は Extensions API とともに Lambda からの invocation event の受信や Lambda への関数の処理結果の送信を HTTP で行う。
* Docker コンテナイメージの構築を容易にするために Runtime API をパッケージ化して、Runtime interface client (RIC) としてリリースされた。
* AWS ベースイメージには RIC が組み込まれているが、カスタムランタイムには組み込まれていないので自分でインストールする必要がある。


Runtime interface emulator (RIE)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
* ローカル環境でのテスト用に `Runtime interface emulator (RIE) <https://github.com/aws/aws-lambda-runtime-interface-emulator>`_ が提供されている。
* Linux、macOS、Windows 用の RIE が提供されている。


参考文献
----------------
- `Runtime support for Lambda container images <https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/runtimes-images.html>`_
- `AWS Lambda の新機能 – コンテナイメージのサポート <https://aws.amazon.com/jp/blogs/news/new-for-aws-lambda-container-image-support/>`_
- `aws/aws-lambda-python-runtime-interface-client <https://github.com/aws/aws-lambda-python-runtime-interface-client>`_
- `aws/aws-lambda-nodejs-runtime-interface-client <https://github.com/aws/aws-lambda-nodejs-runtime-interface-client>`_
- `aws/aws-lambda-ruby-runtime-interface-client <https://github.com/aws/aws-lambda-ruby-runtime-interface-client>`_
- `awslambdaric 1.0.0 <https://pypi.org/project/awslambdaric/>`_
- `AWS Lambda のクォータ <https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/gettingstarted-limits.html>`_
- `AWS Lambda Extensions（プレビュー）のご紹介 <https://aws.amazon.com/jp/blogs/news/introducing-aws-lambda-extensions-in-preview/>`_
- `Amazon Linux AMI <https://aws.amazon.com/jp/amazon-linux-ami/>`_
- `Update on Amazon Linux AMI end-of-life <https://aws.amazon.com/jp/blogs/aws/update-on-amazon-linux-ami-end-of-life/>`_
- `Amazon Linux 2 プレアップグレードアシスタントの発表 <https://aws.amazon.com/jp/about-aws/whats-new/2019/03/announcing_the_amazon_linux_2_preupgrade_assistant/>`_
