[第5回] Amazon SageMaker の基本的な使い方を理解する (1)
========================================================

はじめに
--------------------
| `前回 <https://news.mynavi.jp/itsearch/article/devsoft/4983>`_ は、ML サービスおよび Amazon SageMaker の概要と利用方法についてご説明させていただきました。
| 図を用いて「開発」「学習」「推論」の流れを説明しましたが、今回から数回の記事では、実際に Amazon SageMaker を使ってみたいと思います。
| 下記を理解していただくことを目標に解説をしていきます。

- Amazon SageMaker を使って実際に「開発」を始めるまでの準備作業の流れがわかる。
- Amazon SageMaker を使って、「開発」「学習」「推論」の流れがわかる。


実際に Amazon SageMaker を使う
---------------------------------------
`「機械学習モデルの構築およびトレーニング、デプロイ with Amazon SageMaker」 <https://aws.amazon.com/jp/getting-started/hands-on/build-train-deploy-machine-learning-model-sagemaker/>`_ というチュートリアルがありますので、これをベースに進めたいと思います。


チュートリアルの概要
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
今回利用するチュートリアルの概要を下記に示します。

- Amazon SageMaker を使って、「開発」「学習」「推論」の一連の流れを体験する。
- 学習・推論に利用するデータは、カリフォルニア大学アーバイン校 (UCI) が公開しているオープンデータを利用する。
  「`Bank Marketing Data Set <https://archive.ics.uci.edu/ml/datasets/Bank+Marketing>`_」と呼ばれる「ポルトガルの銀行マーケティングキャンペーン」のデータを利用する。
- データには大きく2種類の情報が記録されている。

  - データ 1：「年齢」や「職業」といった銀行の顧客の属性情報など
  - データ 2：マーケティングを行った結果、その顧客が実際に「預金証書 (CD)」を申し込んだか否かのフラグ

- 「データ 1」から機械学習アルゴリズムによって何らかのパターンを見出して、これから営業をかける顧客 (「データ 2」が未知の顧客) が「預金証書 (CD)」を申し込んでくれそうかどうかを二値分類する機械学習モデルを構築する。
- **「預金証書 (CD) を申し込んでくれそうな顧客」に重点的に営業をかけることによって、営業活動を効率化することができる。顧客名簿の上から順に営業をかけるよりも顧客を獲得できる可能性が高まる。**
- 機械学習アルゴリズムは、Amazon SageMaker の組み込みアルゴリズムとして提供されている「`XGBoost <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/xgboost-72.html>`_」を利用する。


これから数回の記事で実施すること
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| チュートリアルは 30 分以内に実施可能な簡単な内容ですが、「自分が Amazon SageMaker を使って今何をしているのか」「Amazon SageMaker が背後でどのような処理が行っているか」を理解できるよう注意しながら詳しく解説を進めていきます。
| チュートリアルの手順に沿って進めていきます。
  `前回 <https://news.mynavi.jp/itsearch/article/devsoft/4983>`_  の記事で示した「開発」「学習」「推論」と概ね同じ流れであるものの、多少前後する箇所があるので予めご了承ください。

.. list-table::
    :header-rows: 1

    * - 工程
      - 実施内容
      - 連載回
    * - 開発
      - AWS マネジメントコンソールにログインする
      - 第5回
    * - 
      - ノートブックインスタンスを作成する
      - 第5回
    * - 
      - ノートブックを起動する
      - 第5回
    * - 
      - 学習データを格納するための S3 バケットを作成する
      - 第6回
    * - 
      - 学習・推論に利用するデータを準備する
      - 第6回
    * - 
      - 学習用のコードを開発する
      - 第6回
    * - 学習
      - XGBoost (組み込みアルゴリズム) を利用して学習を行う
      - 第6回
    * - 推論
      - 学習済モデルを推論用インスタンスにデプロイする
      - 第7回
    * - 
      - テストデータを使って顧客が申し込みを行ったかを予測する
      - 第7回
    * - 
      - 精度の評価を行う
      - 第7回
    * - 後片付け
      - 作成したリソースを削除する
      - 第7回


前提・注意事項
^^^^^^^^^^^^^^^^^^^^^^^^
| 今回の記事の内容を実施する際の前提と注意事項を下記に示します。
| なお、 **この記事の内容を実施すると数百円程度の課金が発生する可能性があります。無用な課金を避けるために必ず作成したリソースの削除が必要ですが、「後片付け」の手順は第 7 回に掲載予定です。あまり期間を開けずに投稿予定であるため、第 7 回の掲載後に試してください。**

- 本番環境 (商用サービスが稼働する AWS アカウント) ではなく、開発環境や検証環境で実施する。
- AWS アカウントを持っていない場合は、「`Amazon SageMaker をセットアップします <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-set-up.html>`_」を参照して取得・セットアップをする。
  今回は Amazon SageMaker Studio は利用しないため、下記の 2 つの手順を実施する。

  - 「`AWS アカウントを作成する <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-account.html>`_」
  - 「`IAM 管理者ユーザーおよびグループの作成 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-account-user.html>`_」

- 画面や手順は2020年7月11日時点のもの。仕様変更などにより表示内容や設定値に変更が入る可能性がある。
- チュートリアルのリージョンは「バージニア北部 (us-east-1)」を想定している。簡単のために「バージニア北部 (us-east-1)」で実施する。

  - チュートリアルのソースコードには「バージニア北部」リージョンを想定するコードがあるので、これを「東京」リージョン向けに改変すれば実行は可能。

- ノートブックインスタンスの Python と主要なパッケージのバージョン

.. list-table::
    :header-rows: 1

    * - 名前
      - バージョン
    * - Python
      - 3.6.10
    * - Numpy
      - 1.18.1
    * - Pandas
      - 1.0.3
    * - AWS SDK for Python (Boto3)
      - 1.14.16
    * - Amazon SageMaker SDK for Python
      - 1.67.1.post0

- XGBoost はビルトインアルゴリズムとして提供されている リリース 0.72 を利用する。


(開発) AWS マネジメントコンソールにログインする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px


まずは AWS マネジメントコンソールに自身の IAM ユーザでログインします。

.. image:: ../../../images/blog/5th/aws-management-console.png
  :width: 900px


リージョンは「バージニア北部」を利用しますので、その他のリージョンにいる場合は AWS マネジメントコンソールの右上のプルダウンメニューから移動してください。

.. image:: ../../../images/blog/5th/region-check.png
  :width: 600px


(開発) ノートブックインスタンスを作成する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px


| それでは、Amazon SageMaker に移動します。
| 「サービスを検索する」の箇所にある検索バーに「sage」程度を入れると、「Amazon SageMaker」が登場しますので、これをクリックして移動します。

.. image:: ../../../images/blog/5th/search-sagemaker.png
  :width: 900px


下記のいずれかの画面に移動しますが、どちらでも問題ありません。

.. image:: ../../../images/blog/5th/sagemaker-initial.png
  :width: 900px


.. image:: ../../../images/blog/5th/sagemaker-dashboard.png
  :width: 900px


| 学習・推論用のコードの開発環境となる「ノートブックインスタンス」を作成していきます。
| 左側の折りたたみメニューの「ノートブック」をクリックして開き、「ノートブックインスタンス」をクリックします。
| ダッシュボードにいる場合は、画面中央の「概要」の中にある「ノートブックインスタンス」をクリックしても構いません。

.. image:: ../../../images/blog/5th/sagemaker-dashboard-notebook.png
  :width: 900px


| ノートブックインスタンスの画面に移動します。
| 現在はノートブックインスタンスが存在しませんが、ここにそのリージョンに存在するノートブックインスタンスの一覧が表示されます。

.. image:: ../../../images/blog/5th/sagemaker-notebook-instance-initial.png
  :width: 900px


ノートブックインスタンスの作成画面に移動します。

.. image:: ../../../images/blog/5th/sagemaker-notebook-instance-create.png
  :width: 900px

| ノートブックインスタンス作成時の設定項目を下記の表に示します。
| 今回は簡単なチュートリアルですので、原則としてデフォルト値を設定します。各設定値の説明を記しますので、実際の業務で利用する際は要件に従って適切に設定してください。
| 設定が完了したら、画面の最下部にある「ノートブックインスタンスの作成」をクリックします。

.. list-table::
    :header-rows: 1

    * - 大分類
      - 小分類
      - 設定値名
      - 説明
      - デフォルト値
    * - ノートブックインスタンス設定
      - 
      - ノートブックインスタンス名
      - | ノートブックインスタンスの名前を設定する。
        | 最大 63 文字まで設定可能。英数字もしくはハイフン (-) の利用が可能。
        | 1つのAWS リージョンのアカウント内で一意である必要がある。
      - | (空白)
        | 今回は「mynotebook」と設定
    * - 
      - 
      - ノートブックインスタンスのタイプ
      - | ノートブックインスタンスのタイプを設定する。
        | まずは「ml.t2.medium」など小さいインスタンスで試した方が良い。
        | CPU やメモリなどのリソース不足が発生した場合にスケールアップもしくは不足するリソースに応じたインスタンスタイプを選択する。
        | (例) CPUを重点的に増やしたい：コンピューティング最適化 (ml.c5.large など)、 メモリを重点的に増やしたい：メモリの最適化 (ml.r5.large など)
        | `「Amazon SageMaker ML インスタンスタイプ」 <https://aws.amazon.com/jp/sagemaker/pricing/instance-types/>`_
        | `「Amazon SageMaker の料金」 <https://aws.amazon.com/jp/sagemaker/pricing/>`_
      - ml.t2.medium
    * - 
      - 
      - Elastic Inference
      - | GPU リソースをアタッチする。ディープラーニングフレームワーク TensorFlow、 Apache MXNet、PyTorch でサポートされている。 
        | `「Amazon SageMaker Elastic Inference (EI) を使用する」 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/ei.html>`_
      - なし
    * - 
      - 追加設定
      - ライフサイクル設定
      - ノートブックインスタンスの作成時にスクリプトを実行して、ノートブックインスタンスをカスタマイズする。
      - 設定値なし
    * - 
      - 
      - ボリュームサイズ (GB 単位)
      - | ノートブックインスタンスにアタッチする EBS ボリューム (ディスク) のサイズ。
        | ボリュームサイズは 5 GB - 16 TB の範囲で設定可能。 
      - 5
    * - アクセス許可と暗号化
      - 
      - IAM ロール
      - | ノートブックインスタンスに付与する AWS リソースの操作権限を IAM ロールとして設定する。
        | AmazonSageMakerFullAccess を付与する場合、`「AmazonSageMakerFullAccess ポリシー」 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/sagemaker-roles.html#sagemaker-roles-amazonsagemakerfullaccess-policy>`_ に記載されている操作権限が付与される。
      - 新しい IAM ロールの作成
    * - 
      - 
      - ルートアクセス - オプション
      - 
      - 有効化 - ノートブックへのルートアクセス権をユーザーに付与する
    * - 
      - 
      - 暗号化キー - オプション
      - ノートブックインスタンスにアタッチする EBS ボリューム (ディスク) の Amazon KMS の暗号鍵を設定する。
      - カスタム暗号化なし
    * - ネットワーク - オプション
      - 
      - VPC - オプション
      - 
      - 非 VPC
    * - Git リポジトリ - オプション
      - デフォルトのリポジトリ
      - リポジトリ
      - | ノートブックインスタンスの作成時に自動で Git リポジトリをクローンする。
        | 例えば、学習・推論用コードが格納された Git リポジトリやアプリのコードが格納されたリポジトリを自動でクローンし、その分の手間が省ける。
        | デフォルトのリポジトリを1つ、追加のリポジトリを最大3つ設定可能。
      - なし
    * - タグ - オプション
      - 
      - キー、値
      - | キーバリュー形式で値を設定する。
        | (例) Name タグ (Name: *<notebook_instance_name>*)、環境タグ (Environment: Production)
      - (空白)


| ノートブックインスタンスに付与する IAM ロールに関して補足します。
| IAM ロールは AWS のリソースに操作権限を与えるために利用します。
| 例えば、学習用データの取得のためにノートブックインスタンスから Amazon S3 のバケットにアクセスが必要となります。
| IAM ユーザに紐づく認証情報として「アクセスキー ID」と「シークレットキー」がありますが、これをコードにハードコーディングすることはアンチパターンとなります。
| IAM ロールに操作権限を定義してノートブックインスタンスに付与することで、具体的な認証情報を記述せずに操作権限を付与することができます。

- (備忘) 図を入れる。

| 今回新規作成した IAM ロールは「AmazonSageMakerFullAccess ポリシー」が操作権限として与えられています。
| 特に、下記の条件に当てはまる Amazon S3 のバケットとオブジェクトの操作権限が与えられる点に注意してください。


.. list-table::
  :header-rows: 1

  * - 条件
    - 説明
  * - 指定する S3 バケット - オプション
    - | 本項目以外の条件に当てはまらない S3 バケットに対するアクセスを許可する。オプション項目であり、これを設定しないことも可能。
      | - **任意の S3 バケット**
      | (例) my-s3-bucket
      | - **特定の S3 バケット**
      | (例) my-s3-bucket-1, my-s3-bucket-2
      | - **なし**
  * - 名前に「sagemaker」が含まれる任意の S3 バケット
    - | 下記のように S3 バケット名に「sagemaker」を含む S3 バケットにはアクセスが可能。
      | (例) **アクセス可能** : my-sagemaker-s3-bucket、**アクセス不可能** : my-s3-bucket
  * - 名前に「sagemaker」が含まれる任意の S3 オブジェクト
    - | 下記のように S3 バケットに格納されているオブジェクト (ファイル) 名に 「sagemaker」を含むオブジェクトにはアクセスが可能。
      | (例) **アクセス可能** : my-sagemaker-object.csv、**アクセス不可能** : my-object.csv
  * - タグ「sagemaker」と値「true」が含まれる任意の S3 オブジェクトにはアクセスが可能。
    - | キーが「sagemaker」、値が「true」と設定されたオブジェクトにはアクセスが可能。
  * - SageMaker へのアクセスを許可するバケットポリシーを持つ S3 バケット
    - 条件に記載されている通り、バケットポリシーにて Amazon SageMaker のアクセスが許可された S3 バケットにはアクセスが可能。


| 設定値に問題がなければ画面が切り替わり、「成功! ノートブックインスタンスが作成されています。」と表示されます。
| 数分間待って、「ステータス」が「**Pending**」から「**InService**」に変わると、ノートブックインスタンスの作成が完了です。

.. image:: ../../../images/blog/5th/sagemaker-notebook-instance-create-pending.png
  :width: 900px


.. image:: ../../../images/blog/5th/sagemaker-notebook-instance-create-inservice.png
  :width: 900px



(開発) ノートブックを起動する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| ノートブックインスタンスの作成まで完了したので、ノートブックを起動します。

- (備忘) このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px

| Amazon SageMaker のノートブックインスタンスでは、Jupyter Notebook もしくは JupyterLab が利用できます。
| これらがプリインストールされた状態でノートブックインスタンスが作成されており、利用者側でインストールは必要ありません。

| Jupyter Notebook、JupyterLab は `Project Jupyter <https://jupyter.org/index.html>`_ と呼ばれる非営利の OSS プロジェクトにより管理されているプログラミングのツールです。
| Python をはじめとして、R や Spark など様々な言語で利用することができます。
| コードを「セル」と呼ばれるブロックに記載して、少しずつ確認しながら実行することができます。
| また、マークダウンでテキストや画像、数式なども記載することもできますので、従来のテキストベースのソースコードと比較すると背景や設計根拠などを詳細に残すことができます。

- (備忘) サンプルか何かのノートブックの画像を入れる。

| JupyterLab は Jupyter Notebook の後継となるノートブックです。
| このチュートリアルを実施範囲ではどちらを選んでも問題ありません。
| AWS のチュートリアルは Jupyter Notebook を利用していますので、こちらの記事では「JupyterLab」を利用して進めたいと思います。

| 「アクション」の「JupyterLab を開く」をクリックします。
| なお、前者をクリックすると従来からの Jupyter notebook が起動し、後者をクリックすると JupyterLab が起動します。

.. image:: ../../../images/blog/5th/sagemaker-notebook-launch.png
  :width: 900px

| JupyterLab を起動すると、下記のような「Launcher」タブが表示されます。
| Python を中心に様々な実行環境が用意されています。
| このチュートリアルでは、Python 3 の実行環境があれば良いので、「Notebook」の配下にある「conda_python3」をダブルクリックします。

.. image:: ../../../images/blog/5th/sagemaker-jupyterlab-launcher.png
  :width: 900px

| すると、「Untitled.ipynb」というタブに切り替わり、左側のメニューに同名のファイルが表示されます。
| これは Jupyter Notebook のファイルとなります。
| 今回はこの状態のまま進みますが、左側のメニューを右クリックして、「Rename」を選択することによりファイル名を変更することも可能です。

.. image:: ../../../images/blog/5th/sagemaker-jupyterlab-python3-notebook.png
  :width: 900px


実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

| グレーの部分を「セル」と呼び、ここにコードを記述していきます。
| 下記のコードをコピー＆ペーストして、「Run」を押下するか、「Shift」+「Enter」で実行できます。
| この後もいくつかコードを示しますが、同様の方法で実行してください。

.. code-block:: python

  # import libraries
  import boto3, re, sys, math, json, os, sagemaker, urllib.request
  from sagemaker import get_execution_role
  import numpy as np
  import pandas as pd
  import matplotlib.pyplot as plt
  from IPython.display import Image
  from IPython.display import display
  from time import gmtime, strftime
  from sagemaker.predictor import csv_serializer

  # Define IAM role
  role = get_execution_role()
  prefix = 'sagemaker/DEMO-xgboost-dm'
  containers = {'us-west-2': '433757028032.dkr.ecr.us-west-2.amazonaws.com/xgboost:latest',
                'us-east-1': '811284229777.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest',
                'us-east-2': '825641698319.dkr.ecr.us-east-2.amazonaws.com/xgboost:latest',
                'eu-west-1': '685385470294.dkr.ecr.eu-west-1.amazonaws.com/xgboost:latest'} # each region has its XGBoost container
  my_region = boto3.session.Session().region_name # set the region of the instance
  print("Success - the MySageMakerInstance is in the " + my_region + " region. You will use the " + containers[my_region] + " container for your SageMaker endpoint.")


| セルの下側に下記のメッセージが出力されれば成功であり、一旦ノートブックインスタンスとノートブックが正常に作成できたと考えて良いと思います。
| *Success - the MySageMakerInstance is in the us-east-1 region. You will use the 811284229777.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest container for your SageMaker endpoint.*


コードの解説
********************
ここでは「`# import libraries`」と「`# Define IAM role`」から始まる大きく2つの処理に分けられます。

.. code-block:: python

  # import libraries
  import boto3, re, sys, math, json, os, sagemaker, urllib.request
  from sagemaker import get_execution_role
  import numpy as np
  import pandas as pd
  # (以下、省略)

| 「`# import libraries`」から始まる処理では、チュートリアルの実行に必要なライブラリ (モジュール) の読み込み (インポート) を行っています。
| Python でよく利用される「Numpy」や「Pandas」に加えて、「Boto3」と呼ばれる 「`AWS SDK for Python <https://aws.amazon.com/jp/sdk-for-python/>`_」、その名の通りで Amazon SageMaker の Python 向け SDK である「`Amazon SageMaker SDK for Python <https://sagemaker.readthedocs.io/en/stable/>`_」を読み込んでいます。
| Matplotlib など利用しないものも一部含まれますが、ここでは一旦無視しましょう。


.. code-block:: python

  # Define IAM role
  role = get_execution_role()
  prefix = 'sagemaker/DEMO-xgboost-dm'
  containers = {'us-west-2': '433757028032.dkr.ecr.us-west-2.amazonaws.com/xgboost:latest',
                'us-east-1': '811284229777.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest',
                'us-east-2': '825641698319.dkr.ecr.us-east-2.amazonaws.com/xgboost:latest',
                'eu-west-1': '685385470294.dkr.ecr.eu-west-1.amazonaws.com/xgboost:latest'} # each region has its XGBoost container
  my_region = boto3.session.Session().region_name # set the region of the instance


「`# Define IAM role`」から始まる処理では、主に下記を実行しています。

| 1. ノートブックインスタンスにアタッチした IAM ロールの実行権限の取得
| 2. S3 バケットのプレフィックスの設定
| 3. 組み込みアルゴリズム XGBoost のコンテナイメージの ECR レジストリのパスの設定
| 4. リージョンの設定

特に、1 と 3 の処理について見ていきます。

1. ノートブックインスタンスにアタッチした IAM ロールの実行権限の取得
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

  role = get_execution_role()

| ノートブックインスタンスの実行ロールの ARN を取得して、その実行ロールを使用して操作を行います。
| ここでいう「実行ロール」とは、ノートブックインスタンス作成時に新規作成した IAM ロールであり、「AmazonSageMakerFullAccess ポリシー」が操作権限として与えられています。
| 詳細に知りたい方は、「`Amazon SageMaker の実行ロールを取得する <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/automatic-model-tuning-ex-role.html>`_」「`sagemaker.session.get_execution_role(sagemaker_session=None) <https://sagemaker.readthedocs.io/en/stable/api/utility/session.html#sagemaker.session.get_execution_role>`_」を参照してください。


3. 組み込みアルゴリズム XGBoost のコンテナイメージの ECR レジストリのパスの設定
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

  containers = {'us-west-2': '433757028032.dkr.ecr.us-west-2.amazonaws.com/xgboost:latest',
                'us-east-1': '811284229777.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest',
                'us-east-2': '825641698319.dkr.ecr.us-east-2.amazonaws.com/xgboost:latest',
                'eu-west-1': '685385470294.dkr.ecr.eu-west-1.amazonaws.com/xgboost:latest'} # each region has its XGBoost container

| 前回説明したように、Amazon SageMaker では Amazon ECR から学習用コンテナイメージをダウンロードし、学習用インスタンス上でコンテナを起動して学習を行います。
| 「組み込みアルゴリズムによる学習を行う」とは、すなわち、「AWS が管理する学習用コンテナイメージを利用して学習を行う」ということです。

| 上記のコードは AWS が管理する Amazon ECR のレジストリから「XGBoost リリース 0.72 のコンテナイメージで最新 (latest) 」取得するための設定です。
| AWS が管理する Amazon ECR のレジストリはリージョンごとに用意されており、今回は「バージニア北部 (us-east-1)」ですので、「`811284229777.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest`」からコンテナイメージを取得します。
| 「東京 (ap-northeast-1)」で利用する場合は、「`'ap-northeast-1': '501404015308.dkr.ecr.ap-northeast-1.amazonaws.com'`」を追加するなどコードの改変が必要です。

| また、今回は「XGBoost リリース 0.72」を利用していますが、より新しい「XGBoost リリース 0.90」もリリースされています。
| リリース 0.72 と 0.90 とではレジストリのパスが異なりますので、ご注意ください。
| 詳細に知りたい方は、「`組み込みアルゴリズムの共通パラメータ <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/sagemaker-algo-docker-registry-paths.html>`_」を参照してください。
| 今回は組み込みアルゴリズムの中から XGBoost を利用していますが、その他のアルゴリズムについても記載されていますので、応用する際に参照して適切に変更してください。


(開発) 学習・推論に利用するデータを格納するための S3 バケットを作成する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 前のセクションで開発環境であるノートブックインスタンスの作成が完了しました。
| ここでは、学習・推論に利用するデータを格納するための S3 バケットを作成します。
| 作業は引き続き、JupyterLab で行います。

- (備忘) このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px

実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

| 下記のコードをセルにコピー＆ペーストした後に、必ず ** `bucket_name` の `your-s3-bucket-name` の箇所を変更してから** 実行してください。
| S3 バケット名は世界で唯一の値にする必要がありますので、名前が重複するとエラーとなります。
| S3 バケット名には、「AWS アカウント ID (12桁の数字)」を含めるなどすると重複しづらいと思います。検証目的であれば「日付」を入れても良いと思います。

.. code-block:: python

  bucket_name = 'your-s3-bucket-name' # <--- CHANGE THIS VARIABLE TO A UNIQUE NAME FOR YOUR BUCKET
  s3 = boto3.resource('s3')
  try:
      if  my_region == 'us-east-1':
        s3.create_bucket(Bucket=bucket_name)
      else: 
        s3.create_bucket(Bucket=bucket_name, CreateBucketConfiguration={ 'LocationConstraint': my_region })
      print('S3 bucket created successfully')
  except Exception as e:
      print('S3 error: ',e)


コードの解説
********************
| チュートリアルでは AWS SDK for Python (Boto3) を利用してノートブックから S3 バケットを作成する方法が示されています。
| AWS マネジメントコンソールを利用しても同等の作業ができますが、ノートブックで作成した方が後々の開発効率を考えても好ましいと考えられます。
| 作業の慣れの問題でもあると考えられるので、これを機に習得した方が良いと思います。

.. code-block:: python

  s3 = boto3.resource('s3')

| AWS SDK for Python (Boto3) では、 AWS のリソースを操作する方法に「Resources」と「Clients」の2種類があります。
| このチュートリアルでは、抽象度の高い「Resources」の方の API を使って S3 バケットを作成しています。

- `Resources <https://boto3.amazonaws.com/v1/documentation/api/latest/guide/resources.html>`_ : 高レベルのオブジェクト指向インターフェース。Clients と比べて抽象度が高い。
- `Clients <https://boto3.amazonaws.com/v1/documentation/api/latest/guide/clients.html>`_: 低レベルのサービス接続。メソッドはサービス API とほぼ1：1で対応し、すべてのサービスの操作がサポートされる。

.. code-block:: python

  if  my_region == 'us-east-1':
    s3.create_bucket(Bucket=bucket_name)
  else: 
    s3.create_bucket(Bucket=bucket_name, CreateBucketConfiguration={ 'LocationConstraint': my_region })

| `create_bucket <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.create_bucket>`_ メソッドの `CreateBucketConfiguration` という引数では S3 バケットを作成するリージョンを指定します。
| これが省略された場合、作成するリージョンが「バージニア北部 (us-east-1)」となるため、`my_region` の値によって条件分岐をしています。


(開発) 学習・推論に利用するデータを準備する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 前のセクションで学習・推論用のデータを格納するための S3 バケットの作成が完了しました。
| ここでは、インターネット上で公開されているデータをノートブックインスタンスのローカルディスク上にダウンロードして簡単な前処理を行います。
| 作業は引き続き、JupyterLab で行います。

- (備忘) このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px

実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  try:
    urllib.request.urlretrieve ("https://d1.awsstatic.com/tmt/build-train-deploy-machine-learning-model-sagemaker/bank_clean.27f01fbbdf43271788427f3682996ae29ceca05d.csv", "bank_clean.csv")
    print('Success: downloaded bank_clean.csv.')
  except Exception as e:
    print('Data load error: ',e)

  try:
    model_data = pd.read_csv('./bank_clean.csv',index_col=0)
    print('Success: Data loaded into dataframe.')
  except Exception as e:
      print('Data load error: ',e)


上記の実行後に、下記のコードもセルにコピー＆ペーストして実行してください。

.. code-block:: python

  train_data, test_data = np.split(model_data.sample(frac=1, random_state=1729), [int(0.7 * len(model_data))])
  print(train_data.shape, test_data.shape)


コードの解説
********************

.. code-block:: python

  urllib.request.urlretrieve ("https://d1.awsstatic.com/tmt/build-train-deploy-machine-learning-model-sagemaker/bank_clean.27f01fbbdf43271788427f3682996ae29ceca05d.csv", "bank_clean.csv")

| `urllib.request.urlretrieve <https://docs.python.org/ja/3.6/library/urllib.request.html#urllib.request.urlretrieve>`_ メソッドは、引数に指定した URL からファイルをローカルにダウンロードします。
| 引数に示した URL は ドメイン名から AWS の静的ファイルを配布するための CDN サービスである Amazon CloudFront もしくは Amazon S3 と思われます。
| ここからノートブックインスタンスのローカルディスク上に、学習や推論に利用するデータである「Bank Marketing Data Set」をダウンロードしています。

| AWS が配布しているデータは、チュートリアルを進めやすいように前処理の実施済のデータです。
| UCI の Machine Learning Repository で公開されているデータをダウンロードして利用する場合は、そのままの状態では利用できず、機械学習で利用できるようにデータの前処理が必要となりますのでご注意ください。


.. code-block:: python

  model_data = pd.read_csv('./bank_clean.csv',index_col=0)

Pandas の `read_csv <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>`_ メソッドを使って、CSV 形式の「Bank Marketing Data Set」を DataFrame に読み込んでいます。


.. code-block:: python

  train_data, test_data = np.split(model_data.sample(frac=1, random_state=1729), [int(0.7 * len(model_data))])

| Numpy の split メソッドを使って、DataFrame として読み込んだデータ (model_data) を学習データ (train_data) とテストデータ (test_data) を 7:3 の割合で分割しています。
| 更に、Pandas の `sample <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.sample.html>`_ メソッドを使ってランダムにデータを選択することでデータの順序性を排除しています。


(開発) 学習用のコードを開発する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| データの準備まで完了しましたので、ここからは学習用のコードを開発していきます。
| 今回は Amazon SageMaker に`組み込みアルゴリズム <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/algos.html>`_ として用意されている「`XGBoost <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/xgboost.html>`_」と呼ばれる機械学習アルゴリズムを利用します。

| 組み込みアルゴリズムとして提供されている機械学習アルゴリズムは、コンテナイメージとして予め Amazon ECR のレジストリに準備されています。
| 利用者は機械学習アルゴリズムを実装する必要はありません。
| 利用したい機械学習アルゴリズムのコンテナイメージをダウンロードしてコンテナを起動し、学習データを渡すだけで利用することができます。

- (備忘) このセクションでの該当箇所は下記の赤枠です。

.. image:: ../../../images/amazon_sagemaker_notebook_instance_1.png
  :width: 900px

| XGBoost を利用するための準備として、学習用データの並べ替えと Estimator と呼ばれる Amazon SageMaker の学習の設定を行います。
| 下記のコードをセルにコピー＆ペーストして実行してください。

実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  pd.concat([train_data['y_yes'], train_data.drop(['y_no', 'y_yes'], axis=1)], axis=1).to_csv('train.csv', index=False, header=False)
  boto3.Session().resource('s3').Bucket(bucket_name).Object(os.path.join(prefix, 'train/train.csv')).upload_file('train.csv')
  s3_input_train = sagemaker.s3_input(s3_data='s3://{}/{}/train'.format(bucket_name, prefix), content_type='csv')


上記の実行後に、下記のコードもセルにコピー＆ペーストして実行してください。

.. code-block:: python

  sess = sagemaker.Session()
  xgb = sagemaker.estimator.Estimator(containers[my_region],role, train_instance_count=1, train_instance_type='ml.m4.xlarge',output_path='s3://{}/{}/output'.format(bucket_name, prefix),sagemaker_session=sess)
  xgb.set_hyperparameters(max_depth=5,eta=0.2,gamma=4,min_child_weight=6,subsample=0.8,silent=0,objective='binary:logistic',num_round=100)


コードの解説
********************

.. code-block:: python

  pd.concat([train_data['y_yes'], train_data.drop(['y_no', 'y_yes'], axis=1)], axis=1).to_csv('train.csv', index=False, header=False)

| 組み込みアルゴリズムの XGBoost は、学習データを表形式でデータを与える必要があります。
| 学習データの1列目を目的変数に、2列目以降を説明変数 (特徴量) とする必要があります。
| 今回の場合、目的変数は「キャンペーンの結果、顧客が預金証書 (CD) を申し込んだか否か」、説明変数は年齢や職業などの「顧客の属性情報」となります。

| 加工前の状態では、目的変数の列が一番後ろに存在しますることに加えて、「`y_yes` (申し込んだ)」だけでなく、「`y_no` (申し込まなかった)」も存在します。
| Pandas の `concat <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html>`_ を使って、「`y_yes`」を切り出して1列目に、後方に存在する「y_yes」「y_no」を削除 (drop) した表を結合して新たな表を作成しています。
| 組み込みアルゴリズムの XGBoost は、学習データの形式として CSV 形式もしくは libsvm 形式をサポートしていますので、ここでは CSV 形式に変換しています。

- (備忘) 変換前後の DF を示す。


.. code-block:: python

  (
    boto3
      .Session()
      .resource('s3')
      .Bucket(bucket_name)
      .Object(
        os
          .path
          .join(
            prefix,
            'train/train.csv'
          )
      )
      .upload_file('train.csv')
  )

| メソッドチェーンで書かれていて見通しが悪いので、改行してみます。
| 要するに、ここでやりたいことは「学習データを S3 バケットにアップロードする」ことです。

| `Session <https://boto3.amazonaws.com/v1/documentation/api/latest/guide/session.html>`_ クラスは、AWS のリソースを操作するために認証情報を環境変数やプロファイルから取得して設定します。
| その後の処理はメソッド名から推測できると思いますが、`resource` メソッドで S3 を設定し、S3 バケット名とオブジェクト名を指定して学習データをアップローとしています。


.. code-block:: python  
  s3_input_train = sagemaker.s3_input(
    s3_data='s3://{}/{}/train'.format(bucket_name, prefix), 
    content_type='csv'
  )

| 次の処理も改行して記載します。
| Amazon SageMaker SDK for Python の `s3_input <https://sagemaker.readthedocs.io/en/stable/api/utility/inputs.html#sagemaker.inputs.s3_input>`_ クラスのオプジェクトを生成しています。
| ここでは、その名の通り入力となる学習データが格納された S3 バケットと学習データのファイル形式 (CSV) を設定しています。

.. code-block:: python

  sess = sagemaker.Session()

Amazon SageMaker API およびその他の必要な AWS サービスとのやり取りを管理するための Session オブジェクトを作成しています。

.. code-block:: python

  xgb = sagemaker.estimator.Estimator(
    containers[my_region],
    role, 
    train_instance_count=1, 
    train_instance_type='ml.m4.xlarge',
    output_path='s3://{}/{}/output'.format(bucket_name, prefix),
    sagemaker_session=sess
  )

| 上記の処理も改行して記載します。
| Amazon SageMaker SDK for Python の `Estimator <https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html#sagemaker.estimator.Estimator>`_ クラスのオプジェクトを生成しています。

| Estimator クラスにて、学習の設定を行います。
| パラメータ (Estimator クラスの引数) の意味について下記に示します。

.. list-table::
    :header-rows: 1

    * - パラメータ (引数)
      - パラメータの意味
    * - containers
      - | 学習に利用するコンテナイメージの URI を指定する。
        | ここでは、組み込みアルゴリズムの XGBoost リリース 0.72 のコンテナイメージの URI を指定している。
    * - role
      - | Amazon SageMaker にアタッチする IAM ロールの名前もしくは ARN を指定する。
        | ここでは、前段で作成した IAM ロール (AmazonSageMakerFullAccess ポリシーが設定されたもの) を設定している。
    * - train_instance_count
      - | 学習用インスタンス数を設定する。
        | 2 以上を設定することで複数インスタンスでの分散学習の実行が可能である。
        | ただし、独自アルゴリズムを利用する場合は分散学習に対応したコードを開発する必要がある。
    * - train_instance_type
      - | 学習用インスタンスのインスタンスタイプを設定する。
        | ML インスタンスとして様々なタイプが用意されており、学習の特性に応じたものを選択する。
        | ここでは、汎用 (M4) の xlarge を設定している。その他に利用できるインスタンスタイプと料金は下記を参照のこと。
        | - 「`Amazon SageMaker ML インスタンスタイプ <https://aws.amazon.com/jp/sagemaker/pricing/instance-types/>`_」
        | - 「`Amazon SageMaker の料金 <https://aws.amazon.com/jp/sagemaker/pricing/>`_」
    * - output_path
      - | 学習におけるアウトプット (モデルアーティファクトと出力ファイル) の出力先となる S3 バケットを指定する。
        | このパラメータが設定されていない場合は、デフォルトバケットに出力される。
    * - sagemaker_session
      - Session オブジェクトを設定する。

.. code-block:: python

  (
    xgb.set_hyperparameters(
      max_depth=5,
      eta=0.2,
      gamma=4,
      min_child_weight=6,
      subsample=0.8,
      silent=0,
      objective='binary:logistic',
      num_round=100
  )

| 上記の処理も改行して記載します。
| ここでは、XGBoost のハイパーパラメータを設定しています。
| ハイパーパラメータの値は機械学習モデルの精度に直結します。
| Amazon SageMaker にはハイパーパラメータを自動チューニングする機能がありますが、ここでは固定値として上記の値を設定しています。
| XGBoost で設定できるハイパーパラメータとそれぞれの意味は、「`XGBoost リリース 0.72 のハイパーパラメータ <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/xgboost-72.html#xgboost-72-hyperparameters>`_ 」に一覧でまとめられていますので、知りたい方は参照してください。

(学習) XGBoost (組み込みアルゴリズム) を利用して学習を行う
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
学習の設定まで完了しましたので、ここからは実際に学習を行います。


実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  xgb.fit({'train': s3_input_train})

| なお、学習が開始されると下記の画像に示したような実行ログが出力されます。
| 1 画面に収まらない量が出力されますが、ただの実行ログですので問題ありません。

.. image:: ../../../images/sagemaker-training-start.png
  :width: 900px

| 学習が完了すると、下記のように完了を示すメッセージが出力されます。
| このチュートリアルはデータ量が少ないため、数分で完了します。

.. image:: ../../../images/sagemaker-training-complete.png
  :width: 900px


コードの解説
********************
| Amazon SageMaker における学習の実行は、`fit <https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html#sagemaker.estimator.Estimator.fit>`_ メソッドに学習データが格納されている S3 バケットの所在を渡すだけで実行できます。
| fit メソッドを実行すると、学習ジョブが起動して XGBoost による機械学習モデルが構築されます。

| 実行ログを簡単に見てみましょう。
| Amazon SageMaker が背後で行っている処理を確認することができます。

.. list-table::
    :header-rows: 1

    * - 実行ログ
      - 処理内容
    * - 2020-07-11 11:34:39 Starting - Starting the training job...
      - 学習ジョブの開始
    * - 2020-07-11 11:34:42 Starting - Launching requested ML instances......
      - 学習用インスタンスの起動開始
    * - 2020-07-11 11:35:57 Starting - Preparing the instances for training......
      - 学習を開始するためのインスタンスの準備開始
    * - 2020-07-11 11:36:53 Downloading - Downloading input data
      - S3 バケットから学習データをダウンロード中
    * - 2020-07-11 11:37:39 Training - Downloading the training image...
      - トレーニングに利用する XGBoost の Docker コンテナイメージのダウンロード中
    * - 2020-07-11 11:37:39 Uploading - Uploading generated training model
      - 学習が完了し、アーティファクトを S3 バケットにアップロード中
    * - Arguments: train
      - 学習の実行を示すメッセージ
    * - [2020-07-11:11:37:34:INFO] Running standalone xgboost training.
      - | XGBoost による学習の実行ログ
        | 
    * - (中略)
      - 
    * - 2020-07-11 11:37:46 Completed - Training job completed
      - 学習ジョブの完了
    * - Training seconds: 53
      - トレーニングに要した時間 (秒)
    * - Billable seconds: 53
      - 課金される時間 (秒)

Amazon SageMaker は従量課金性であり、今回のトレーニングでは 53 秒分の課金がされることがわかります。

- (メモ) ビルトインアルゴリズムを利用することでコーディング量が減る。


(推論) 学習済モデルを推論用インスタンスにデプロイする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 学習を実行し、機械学習モデルの構築まで完了しました。
| ここでは機械学習モデルをデプロイします。
| デプロイとは、API サーバの構築。推論用インスタンスの作成、推論用コンテナとして機械学習モデルを起動する。


実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  xgb_predictor = xgb.deploy(initial_instance_count=1,instance_type='ml.m4.xlarge')

なお、デプロイが開始されると、下記の画像に示したように処理の進行とともに「- (ハイフン)」が出力されていきます。

.. image:: ../../../images/sagemaker-deploy-start.png
  :width: 900px

デプロイが完了すると、下記のように完了を示す「!」が出力されます。
デプロイ処理は数分で完了します。

.. image:: ../../../images/sagemaker-deploy-complete.png
  :width: 900px


コードの解説
********************

.. code-block:: python

  xgb_predictor = xgb.deploy(
    initial_instance_count=1,
    instance_type='ml.m4.xlarge'
  )

| この処理も改行して記載します。
| `deploy <https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html#sagemaker.estimator.Estimator.deploy>`_ メソッドを使って、機械学習モデルのデプロイを実行します。
| ここで設定するパラメータ (引数) とそれぞれの意味を下記に示します。

.. list-table::
    :header-rows: 1

    * - パラメータ (引数)
      - パラメータの意味
    * - initial_instance_count
      - | 推論用インスタンス数を設定する。
    * - instance_type
      - | 推論用インスタンスのインスタンスタイプを設定する。
        | ML インスタンスとして様々なタイプが用意されており、学習の特性に応じたものを選択する。
        | ここでは、汎用 (M4) の xlarge を設定している。その他に利用できるインスタンスタイプと料金は下記を参照のこと。
        | - 「`Amazon SageMaker ML インスタンスタイプ <https://aws.amazon.com/jp/sagemaker/pricing/instance-types/>`_」
        | - 「`Amazon SageMaker の料金 <https://aws.amazon.com/jp/sagemaker/pricing/>`_」

| 推論用インスタンスを作成して、S3 バケットから学習済の機械学習モデルを、Amazon ECR から推論用コンテナイメージをダウンロードして、推論用コンテナを起動します。
| アプリなどからアクセスするためのエンドポイントを作成しています。エンドポイントには HTTPS で接続します。


(推論) テストデータを使って顧客が申し込みを行ったかを予測する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  test_data_array = test_data.drop(['y_no', 'y_yes'], axis=1).values #load the data into an array
  xgb_predictor.content_type = 'text/csv' # set the data type for an inference
  xgb_predictor.serializer = csv_serializer # set the serializer type
  predictions = xgb_predictor.predict(test_data_array).decode('utf-8') # predict!
  predictions_array = np.fromstring(predictions[1:], sep=',') # and turn the prediction into an array
  print(predictions_array.shape)

実行した結果出力される `(12357,)` は、予測した顧客の数 (12,357人) です。


コードの解説
********************

.. code-block:: python

  test_data_array = test_data.drop(['y_no', 'y_yes'], axis=1).values #load the data into an array

| 前段でデータを学習データとテストデータに分割しました。
| その際には単純に分割しただけで、テストデータには正解の値 (「y_yes」と「y_no」) が含まれます。
| 顧客の属性情報から申し込みを行うか否かを予測したいので、正解の値を削除 (drop) しています。

.. code-block:: python

  xgb_predictor.content_type = 'text/csv' # set the data type for an inference
  xgb_predictor.serializer = csv_serializer # set the serializer type

推論に用いるテストデータの Content type とシリアライザのタイプに CSV 用のものを指定しています。

.. code-block:: python

  predictions = xgb_predictor.predict(test_data_array).decode('utf-8') # predict!

テストデータを推論エンドポイントに送信して、推論結果を得ています。

.. code-block:: python

  predictions_array = np.fromstring(predictions[1:], sep=',') # and turn the prediction into an array

推論結果がカンマ区切りのテキストデータで返されるので、後続の精度の評価をするために Numpy の `fromstring <https://numpy.org/doc/1.18/reference/generated/numpy.fromstring.html>`_ メソッドを使って Array に変換しています。


(推論) 精度の評価を行う
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 推論結果を得ることができましたので、最後に精度の評価を行います。
| 二値分類の精度評価においてよく利用される「混同行列 (confusion matrix)」を使います。

- (メモ) ROC 曲線、AUC を利用しても良いかも。
- (メモ) アプリからの接続方法についても言及する。

実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  cm = pd.crosstab(index=test_data['y_yes'], columns=np.round(predictions_array), rownames=['Observed'], colnames=['Predicted'])
  tn = cm.iloc[0,0]; fn = cm.iloc[1,0]; tp = cm.iloc[1,1]; fp = cm.iloc[0,1]; p = (tp+tn)/(tp+tn+fp+fn)*100
  print("\n{0:<20}{1:<4.1f}%\n".format("Overall Classification Rate: ", p))
  print("{0:<15}{1:<15}{2:>8}".format("Predicted", "No Purchase", "Purchase"))
  print("Observed")
  print("{0:<15}{1:<2.0f}% ({2:<}){3:>6.0f}% ({4:<})".format("No Purchase", tn/(tn+fn)*100,tn, fp/(tp+fp)*100, fp))
  print("{0:<16}{1:<1.0f}% ({2:<}){3:>7.0f}% ({4:<}) \n".format("Purchase", fn/(tn+fn)*100,fn, tp/(tp+fp)*100, tp))


コードの解説
********************
Pandas の `crosstab <https://pandas.pydata.org/pandas-docs/version/1.0.3/reference/api/pandas.crosstab.html>`_ 関数を使って、推論結果のクロス集計を行っています。

ここで、精度評価で利用している混同行列について補足します。

.. image:: ../../../images/confusion-matrix.png
  :width: 450px

| 行方向 (縦軸) が「正解 (観測; Observed)」、列方向 (横軸) が「予測 (Predicted)」を表します。
| 正解 (2種類) * 予測 (2種類) で、下記に示す 4 つの指標があります。
| 混同行列という名前の通りで混乱しやすいですが、Positive/Negative はあくまで「予測」に対してかかります。
| その予測の正解と不正解により、True/Negative が付いていると考えると理解しやすいと思います。

- True Positive (TP): 預金証書 (CD) を申し込むと予測して、実際に申し込んだ顧客の数
- False Poritive (FP): 預金証書 (CD) を申し込むと予測したが、実際には申し込まなかった顧客の数
- True Negative (TN): 預金証書 (CD) を申し込まないと予測して実際に申し込まなかった顧客の数
- False Negative (FN): 預金証書 (CD) を申し込まないと予測したが、実際には申し込んだ顧客の数


推論結果についての考察
******************************
上記のコードを実行すると、下記のような出力結果を得られます。

.. image:: ../../../images/sagemaker-predict-confusion-matrix.png
  :width: 450px

| `Overall Classification Rate` で示されている数値が正解率であり、今回は 89.5% でした。
| True Negative が 90% である一方で、True Poritive は 65% とかなり低いように見えます。
| なぜでしょうか？

| 学習データ (train_data) には、28,831 人分の顧客データを利用しました。
| このうち、預金証書 (CD) を申し込んだ顧客数 (`y_yes` が 1 の合計) と申し込まなかった (`y_no` が 1 の合計) を確認してみます。

.. image:: ../../../images/sagemaker-predict-train-data.png
  :width: 450px

| 前者が 3,219 人であることに対して、後者は 25,612 人と 5 倍近い差があります。
| 預金証書 (CD) を申し込んだ顧客数のデータを増やすと、True Positive の割合も高くなり、精度を高められる可能性があります。
| 二値分類においては、学習データとなる正解データは十分な量を同程度用意することが望ましいと言われています。
| 今回のチュートリアルでもその一端を垣間見ることができました。


(後片付け) 作成したリソースを削除する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 以上で「開発」「学習」「推論」を一通り実施することができました。
| 課金対象のリソースがありますので、課金を防ぐためにリソースを削除します。

- (メモ) 任意で実施。課金が気になる場合は必ず実施する。
- (メモ) 課金されるリソース・課金されないリソースを書いておく。

実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  sagemaker.Session().delete_endpoint(xgb_predictor.endpoint)
  bucket_to_delete = boto3.resource('s3').Bucket(bucket_name)
  bucket_to_delete.objects.all().delete()


コードの解説
********************
上記のコードを実行することによって、下記が実行されます。

- 推論用エンドポイントの削除
- S3 バケット内のオブジェクトの削除


その他のリソースの削除
*********************************
この他にも「ノートブックインスタンス」と「CloudWatch Logs」が課金対象となりますので、削除忘れに注意してください。
ノートブックインスタンスは「停止」と「削除」の 2 段階での対応が必要です。

| まず、ノートブックインスタンスの削除を行います。
| JupyterLab のタブは、Web ブラウザの「×」ボタンなどで閉じてください。

| AWS マネジメントコンソールのノートブックインスタンスの画面に移動します。
| 削除対象のノートブックインスタンスをラジオボタンで選択し、「アクション」で「停止」を選択します。
| 停止処理に移ると、「ステータス」が「InService」から「Stopping」に移行しますので、数分待ちます。

.. image:: ../../../images/sagemaker-notebook-delete-1.png
  :width: 900px


「ステータス」が「Stopped」になったことを確認して、「アクション」で「削除」をクリックします。

.. image:: ../../../images/sagemaker-notebook-delete-2.png
  :width: 900px


ノートブックインスタンスの削除を確認するウィンドウが表示されますので、「削除」をクリックします。

.. image:: ../../../images/sagemaker-notebook-delete-3.png
  :width: 450px


| 「ステータス」が「Deleting」に移行し、数分待つとノートブックインスタンスが画面からなくなります。
| これでノートブックインスタンスの削除は完了です。


| 次に、CloudWatch Logs の削除を行います。
| AWS マネジメントコンソールのトップ画面などから「CloudWatch」を検索して移動します。

.. image:: ../../../images/cloudwatch-logs-delete-1.png
  :width: 900px


CloudWatch の画面に移動できたら、左側のメニューから「ロググループ」を選択します。

.. image:: ../../../images/cloudwatch-logs-delete-2.png
  :width: 450px


| ロググループの画面に移動できたら、検索バーに「sagemaker」と入力してログを検索します。
| 削除するログを全て選択して、「アクション」で「削除」をクリックします。

.. image:: ../../../images/cloudwatch-logs-delete-3.png
  :width: 900px


ロググループの削除を確認するウィンドウが表示されますので、「削除」をクリックします。

.. image:: ../../../images/cloudwatch-logs-delete-4.png
  :width: 450px


ロググループが画面からなくなったら削除は完了です。

| S3 バケットと IAM ロールも作成しましたが、これらは存在するだけでは課金されません。
| それぞれのサービスの画面から必要に応じて削除してください。


考察
----------------
- (メモ) ここまででかなりの長さになることが予想されるので、連載を分けた方が良いかも。
- (メモ) 自分のビジネス課題に応用するにはどうすれば良いのか？
- (メモ) XGBoost の詳細を知る必要があるか？ (下記あたりを抑えておけば良いのでは？)

  - どんな課題に適用できるか？
  - どんなデータを準備すれば良いか？
  - どうやって利用するか？


まとめ
--------------------
| 今回の記事では、Amazon SageMaker を実際に使って開発・学習・推論をする方法についてご説明させていただきました。
| 次回は、Amazon SageMaker Studio の使い方についてみていきたいと思います。


参考文献
-----------------
- `「機械学習モデルの構築およびトレーニング、デプロイ with Amazon SageMaker」 <https://aws.amazon.com/jp/getting-started/hands-on/build-train-deploy-machine-learning-model-sagemaker/>`_
- `「Amazon SageMaker 開発者ガイド」 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/whatis.html>`_
- `「Amazon SageMaker ML インスタンスタイプ」 <https://aws.amazon.com/jp/sagemaker/pricing/instance-types/>`_
- `「Amazon SageMaker の料金」 <https://aws.amazon.com/jp/sagemaker/pricing/>`_


+++++++++++

.. include:: ../author/author.rst
