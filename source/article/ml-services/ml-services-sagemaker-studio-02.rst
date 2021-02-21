[第10回] Amazon SageMaker Studio の基本的な使い方 (2)
===================================================================

はじめに
-------------------------------------------------------------------

前回は XXX について解説しました。

* Amazon SageMaker Studio の使い方がわかる。
* Amazon SageMaker Autopilot の概要と使い方がわかる。


実際に Amazon SageMaker Studio を使う
-------------------------------------------------------------------

今回も解説の題材に AWS 社が公開するチュートリアルを利用します。

    * `「機械学習モデルを自動的に作成する Amazon SageMaker Autopilot を使用」 <https://aws.amazon.com/jp/getting-started/hands-on/create-machine-learning-model-automatically-sagemaker-autopilot/>`_

このチュートリアルでは、「Amazon SageMaker Autopilot」をはじめとする新機能を利用します。
チュートリアルで利用する新機能についても紹介していきます。

なお、Amazon SageMaker Studio は、2020 年 11 月に東京リージョンでの提供開始が発表されました。
今回の記事では、東京リージョンを利用して進めていきます。

    * `Amazon SageMaker Studio is now expanded to AWS regions worldwide <https://aws.amazon.com/jp/about-aws/whats-new/2020/11/amazon-sagemaker-studio-is-expanded-to-aws-regions-worldwide/>`_


チュートリアルの概要
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

このチュートリアルで扱う課題とデータセットは、第 5 〜 8 回目の連載と同一です。
「ポルトガル銀行のダイレクトマーケティングのデータ」を利用して、顧客の属性データから顧客が定期預金を「申し込む」か「申し込まないか」の二値分類を行います。
機械学習を使って見込み顧客を予測できることにより、営業活動を効率化することができます。

第 5 回目の連載で背景を説明していますので、必要に応じて確認してください。

    * `「【第5回】Amazon SageMaker の基本的な使い方 (1)」 <https://news.mynavi.jp/itsearch/article/devsoft/5099>`_


チュートリアルの流れを下記に示します。

.. list-table::
    :header-rows: 1

    * - 工程
      - ステップ
      - 枝番
      - 実施内容
      - 連載回
    * - 準備
      - 1
      - 
      - AWS アカウントを今すぐ無料で作成
      - 第 10 回
    * - 開発
      - 2
      - 
      - Amazon SageMaker Studio をセットアップする
      - 
    * -
      - 3
      - 
      - データセットをダウンロードする
      - 
    * - 学習
      - 4
      - 
      - SageMaker Autopilot 実験を作成する
      - 
    * - 
      - 5
      - 
      - SageMaker Autopilot 実験のさまざまなステージを調べる
      - 
    * - 推論
      - 6
      - 
      - 最適なモデルをデプロイする
      - 
    * - 
      - 7
      - 
      - モデルを使用して予測を行う
      - 
    * - 
      - 8
      - 
      - クリーンアップ
      - 


注意事項
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

今回の解説の題材に使うチュートリアルを試す場合、下記に注意してください。
特に、課金が発生する点に注意してください。

* 想定外の影響を避けるため、本番環境 (商用サービスが稼働するAWSアカウント) ではなく、開発環境や検証環境で実施してください
* チュートリアルの最終更新日が「2020 年 5 月 12 日」となっています。Amazon SageMaker は 2020 年 11 〜 12 月に大幅な機能アップデートがありましたが、その内容がまだ取り込まれておらず、チュートリアルの画面の一部が古いものがあります
* チュートリアルの「時間」に「10分」と記載されていますが、実際には Amazon SageMaker Autopilot のジョブ実行による待ち時間を含めると 2 時間程度はかかります。時間に余裕をもって実行してください
* Amazon SageMaker Autopilot により数多くのジョブが起動されます。無償枠を超えて 10 ドル未満の課金が発生する可能性があります


ステップ 1 : AWS アカウントを今すぐ無料で作成
-------------------------------------------------------------------

**このステップは、AWS アカウントを持っていない場合にのみ実施してください。**
開発者ガイドの「Amazon SageMakerをセットアップします」を参照して、AWS アカウントの取得とセットアップを実施してください。

    * `AWS アカウントを作成する <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-set-up.html#gs-account>`_
    * `IAM 管理者ユーザーおよびグループの作成 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-set-up.html#gs-account-user>`_


ステップ 2 : Amazon SageMaker Studio をセットアップする
-------------------------------------------------------------------

AWS マネジメントコンソールにログインして、リージョンが「東京」であることを確認してください。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-region.jpg
  :width: 900px


AWS マネジメントコンソールの上部の検索ウィンドウに「sagamaker」などと入力して、Amazon SageMaker のコンソールに移動します。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-search.jpg
  :width: 900px


左側のメニューの「Amazon SageMaker Studio」もしくは「今すぐ始める」の配下にある「SageMaker Studio」のいずれかを選択します。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-console.jpg
  :width: 900px


下図のようなセットアップ画面に移動します。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-quick-start.jpg
  :width: 900px

Amazon SageMaker Studio のセットアップ方法について補足します。

| セットアップ方法には、下記の2つがあります。
| ここではチュートリアルに従って、「クイックスタート」でセットアップを進めます。
| なお、IAM 認証を選択して構築したドメイン (Amazon SageMaker Studio の環境) を AWS SSO 認証に変更する場合は、一度ドメインを削除して再作成が必要になります。特に、商用で利用する際は注意して選択してください。

.. list-table::
    :header-rows: 1

    * - セットアップ方法
      - 説明
    * - クイックスタート
      - | * Amazon SageMaker Studio の操作に必要なアカウントや権限設定の大部分を Amazon SageMaker に任せる。
        | * 認証方法は IAM となる。
    * - 標準セットアップ
      - | * Amazon SageMaker Studio の操作に必要なアカウントや権限設定を詳細に行う場合に選択する。
        | * 認証方法は IAM に加えて、AWS SSO が選択可能である。

| 「クイックスタート」での設定項目やその意味について、下記の表にまとめます。
| なお、今回は検証ですので、特に理由がない限りはデフォルト値のまま進めることとします。

.. list-table::
    :header-rows: 1

    * - 設定項目
      - 説明
      - デフォルト値
    * - ユーザー名
      - | **Amazon SageMaker Studio のユーザ作成**
        | Amazon SageMaker Studio で利用するユーザを作成する
      - | default-xxxxxxxxxxxxx
        | (ハイフン以降は自動で採番)
    * - 実行ロール
      - | **上記のユーザに付与する AWS リソースの操作権限の設定**
        | 本表の下に補足情報を記載するので、そちらも参照されたい
      - | IAM ロールを選択する
        | (新しいIAMロールの作成)
    * - SageMaker プロジェクトと JumpStart
      - | **Amazon SageMaker Pipelines と Amazon SageMaker JumpStart の利用可否の設定**
        | Amazon SageMaker Pipelines (MLOps プロジェクト) および Amazon SageMaker JumpStart の利用可否を設定する。
        | ※ (注意) 上記は AWS Service Catalog や AWS CloudFormation を利用して実装されており、それぞれの利用するリソースへのアクセス許可を設定する。
      - 有効


ここで作成される IAM ロールは、`「第5回の連載」 <https://news.mynavi.jp/itsearch/article/devsoft/5099>`_ で作成したものと同様となります。
「AmazonSageMakerFullAccess」と、下記で設定した条件に当てはまる Amazon S3のバケットとオブジェクト(ファイル)の操作権限を持つ IAM ロールが作成されます。
今回は下図の設定で IAM ロールを作成しますが、商用で利用する場合は必要最低限の権限を付与してください。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-quick-start-iam-role.jpg
  :width: 600px

「送信」をクリックして、下記に示す「SageMaker Studio コントロールパネル」が表示されればセットアップは完了です。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-control-panel.jpg
  :width: 900px

なお、著者の AWS アカウントでは、下図のようなエラーが発生しました。
プロジェクトの「起動制約ロール」と「製品使用ロール」が存在しないことを示すエラーです。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-control-panel-error.jpg
  :width: 900px

しかし、「SageMaker Studio コントロールパネル」の図の赤枠内に示したようにどちらのロールも正常にアタッチされています。
このチュートリアルではプロジェクトは使わないため問題ありませんので、気にせずに進めてください。


ステップ 3 : データセットをダウンロードする
-------------------------------------------------------------------

「Studio を開く」

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step3-open-sagemaker-studio.jpg
  :width: 900px


「Select a SageMaker Image」に「Data Science」を選択

「Notebook」を選択

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step3-sagemaker-studio-top.jpg
  :width: 900px

ノートブックの右上の赤枠が「Unknown」の場合は、これを選択

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step3-sagemaker-notebook-unknown.jpg
  :width: 900px


今回のチュートリアルでは小さいインスタンスで十分であるため、「ml.t3.medium」を選択

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step3-sagemaker-notebook-kernel.jpg
  :width: 900px


インスタンスの起動に成功すると、「Unknown」が選択したインスタンスタイプの vCPU とメモリサイズが表示が変わります。
「ml.t3.medium」の場合は、「2 vCPU + 4 GiB」と表示されます。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step3-sagemaker-notebook.jpg
  :width: 900px


ノートブックのセルに下記のコードをコピー＆ペーストして実行してください。
実行はノートブック上部にある ▷ ボタンか、Shift キー＋Enter キーで行ってください。

.. code-block:: python

    %%sh
    apt-get install -y unzip
    wget https://sagemaker-sample-data-us-west-2.s3-us-west-2.amazonaws.com/autopilot/direct_marketing/bank-additional.zip
    unzip -o bank-additional.zip


amazon-sagemaker-autopilot-tutorial-step3-download



ノートブックのセルに下記のコードをコピー＆ペーストして実行してください。

.. code-block:: python
    
    import pandas as pd
    data = pd.read_csv('./bank-additional/bank-additional-full.csv')
    data[:10]

下記のように表形式でデータが表示されれば成功です。



ここでは、Pandas の read_csv メソッドを使って、ダウンロードした CSV 形式のファイルの最初の10行を出力しています。




ノートブックのセルに下記のコードをコピー＆ペーストして実行してください。

.. code-block:: python
    
    import sagemaker

    prefix = 'sagemaker/tutorial-autopilot/input'
    sess   = sagemaker.Session()

    uri = sess.upload_data(path="./bank-additional/bank-additional-full.csv", key_prefix=prefix)
    print(uri)


下記のように S3 バケットの URI が表示されれば成功です。

.. code-block::
    
    s3://sagemaker-ap-northeast-1-ACCOUNT_NUMBER/sagemaker/tutorial-autopilot/input/bank-additional-full.csv

「ACCOUNT_NUMBER」にはご自身の AWS アカウントの 12 ケタの数字が入ります。


ステップ 4 : SageMaker Autopilot 実験を作成する
-------------------------------------------------------------------

Autopilot から前処理・学習を行う「実験」を作成する
背後で Amazon SageMaker Experiment と連携して実現されている。

Amazon SageMaker Studio のアップデートにより、チュートリアルの画面イメージと異なるので注意してください。

左側のナビゲーションペインで、下図に示す三角形のアイコンを選択します。
続けて、「Project」と表示されているプルダウンメニューから、「Experiments and trials」を選択します。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step4-select-project.jpg
  :width: 900px


「Create Experiment」を選択します。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step4-create-experiment.jpg
  :width: 900px


ここで、「AUTOPILOT EXPERIMENT SETTINGS」で実験の設定を行います。
今回は簡単なチュートリアルですので、原則としてデフォルト値を設定 します。
各設定値の説明を簡単に記しますので、実際の業務で利用する際は参考にしてください。

.. list-table::
    :header-rows: 1

    * - 設定値名
      - 説明
      - デフォルト値
    * - Experiment name
      - 実験の名称を設定する。
      - なし (今回は「tutorial-autopilot」と設定)
    * - TAGS - Optional
      - 実験に付与するタグをキーバリュー形式で設定する。オプション設定であるため、必要に応じて設定する。
      - なし
    * - Project
      - 踏襲するプロジェクトがある場合に設定する。オプション設定であるため、必要に応じて設定する。
      - なし
    * - CONNECT YOUR DATA
      - 
      - なし
    * - S3 bucket address
      -
      -
    * - Is your S3 input a manifest file?
      -
      -
    * - Target
      -
      -
    * - Output data location (S3 bucket)
      -
      -
    * - S3 bucket name
      -
      -
    * - Dataset directory name
      -
      -
    * - Select the machine learning problem type
      -
      -
    * - Do you want to run a complete experiment?
      - |
        | Yes
        | No, run a pilot to create a notebook with candidate definitions
      -
    * - IAM role
      -
      - Default SageMaker Role
    * - Encryption key - Optional
      - 
      - なし
    * - Virtual private cloud (VPC) - Optional
      -
      - なし
    * - Max trial runtime in seconds
      -
      - 0
    * - Max job runtime in seconds
      -
      - 0
    * - Max candidates
      -
      - 0


入力が終わったら、「Create Experiment」を選択します。

なお、実験の完了までに2時間程度かかる点に注意してください。
途中経過を観察する必要はありませんが、放置が許されない場合や余分な課金を防ぐために実験完了後に Amazon SageMaker Notebooks のインスタンスを停止させたい場合もあるかと思いますので、時間に余裕を持って実施してください。


ステップ 5 : SageMaker Autopilot 実験のさまざまなステージを調べる
-------------------------------------------------------------------


.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-initial.jpg
  :width: 900px

Amazon SageMaker Autopilot が下記の4つのタスクを自動で実行します。

1. Pre-processing
2. Candidate Definitions Generated
3. Feature Engineering
4. Model Tuning


1. Pre-processing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


2. Candidate Definitions Generated
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-after-candidate-definitions-generated.jpg
  :width: 900px


.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-candidate-definition-notebook-1.jpg
  :width: 900px


.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-data-exploration-notebook-1.jpg
  :width: 900px



3. Feature Engineering
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^




4. Model Tuning
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-model-tuning.jpg
  :width: 900px



実験が完了すると、下記のようにアニメーションが消えて、結果の表示のみとなります。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step5-after-completed.jpg
  :width: 900px


ステップ 6 : 最適なモデルをデプロイする
-------------------------------------------------------------------


ステップ 7 : モデルを使用して予測を行う
-------------------------------------------------------------------


ステップ 8 : クリーンアップ
-------------------------------------------------------------------


まとめ
-------------------------------------------------------------------

今回は XXX について解説をしました。次回は YYY について扱いたいと思います。


+++++++++++

.. include:: ../author/author.rst
