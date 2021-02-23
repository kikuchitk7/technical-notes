[第10回] Amazon SageMaker Studio の基本的な使い方 (2)
===================================================================

はじめに
-------------------------------------------------------------------

`前回 <https://news.mynavi.jp/itsearch/article/devsoft/5232>`_ は Amazon SageMaker Studio の概要について解説しました。
今回は実際に Amazon SageMaker Studio を使ってみたいと思います。

下記を理解していただくことを目標にして解説をしていきます。

* Amazon SageMaker Studio の使い方がわかる。
* Amazon SageMaker Autopilot の概要と使い方がわかる。


実際に Amazon SageMaker Studio を使う
-------------------------------------------------------------------

今回も AWS 社が公開するチュートリアルを利用して解説を進めていきます。

    * `「機械学習モデルを自動的に作成する Amazon SageMaker Autopilot を使用」 <https://aws.amazon.com/jp/getting-started/hands-on/create-machine-learning-model-automatically-sagemaker-autopilot/>`_

このチュートリアルでは、Amazon SageMaker Studio に加えて、下記の機能も体験することができます。

* Amazon SageMaker Notebooks: Amazon SageMaker Studio に統合されたフルマネージドなノートブック
* Amazon SageMaker Autopilot: AutoML 機能による機械学習モデルの自動構築
* Amazon SageMaker Experiments: 学習ジョブの実行履歴や設定、結果を一元管理

| なお、Amazon SageMaker Studio は、2020 年 11 月に東京リージョンでの提供開始が発表されました。
| チュートリアルはバージニア北部リージョンを利用する手順となっていますが、今回の記事では東京リージョンを利用して進めていきます。

    * `Amazon SageMaker Studio is now expanded to AWS regions worldwide <https://aws.amazon.com/jp/about-aws/whats-new/2020/11/amazon-sagemaker-studio-is-expanded-to-aws-regions-worldwide/>`_


チュートリアルの概要
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

このチュートリアルで扱う課題とデータセットは、第5〜8回目の連載と同一です。

| 「ポルトガル銀行のダイレクトマーケティングのデータ」を利用して、顧客の属性データから顧客が定期預金を「申し込む」か「申し込まないか」の二値分類を行います。
| 機械学習を使って見込み顧客を予測できることにより、営業活動の効率化を図ることができます。

`第5回目の連載 <https://news.mynavi.jp/itsearch/article/devsoft/5099>`_ で背景を説明していますので、必要に応じて確認してください。

チュートリアルの流れを下記に示します。
解説のボリュームが多いため、これからの3回に渡って解説を行います。

.. list-table::
    :header-rows: 1

    * - 工程
      - ステップ
      - 実施内容
      - 連載回
    * - 準備
      - 1
      - AWS アカウントを今すぐ無料で作成
      - 第10回
    * - 開発
      - 2
      - Amazon SageMaker Studio をセットアップする
      - 第10回
    * -
      - 3
      - データセットをダウンロードする
      - 第11回
    * - 学習
      - 4
      - SageMaker Autopilot 実験を作成する
      - 第11回
    * - 
      - 5
      - SageMaker Autopilot 実験のさまざまなステージを調べる
      - 第11回
    * - 推論
      - 6
      - 最適なモデルをデプロイする
      - 第12回
    * - 
      - 7
      - モデルを使用して予測を行う
      - 第12回
    * - 
      - 8
      - クリーンアップ
      - 第12回


注意事項
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

今回の解説の題材に使うチュートリアルを試す場合、下記に注意してください。
特に、課金が発生する点に注意してください。

* 想定外の影響を避けるため、本番環境 (商用サービスが稼働するAWSアカウント) ではなく、開発環境や検証環境で実施してください
* チュートリアルの最終更新日が「2020年5月12日」となっています。Amazon SageMaker は2020年11〜12月に大幅な機能アップデートがありましたが、その内容がまだ取り込まれておらず、チュートリアルの画面の一部が古いものがあります
* チュートリアルの「時間」に「10分」と記載されていますが、実際には Amazon SageMaker Autopilot のジョブ実行による待ち時間を含めると2時間程度はかかります。時間に余裕をもって実行してください
* Amazon SageMaker Autopilot により数多くのジョブが起動されます。無償枠を超えて10ドル未満の課金が発生する可能性があります


ステップ 1 : AWS アカウントを今すぐ無料で作成
-------------------------------------------------------------------

| **このステップは、AWS アカウントを持っていない場合にのみ実施してください。**
| 開発者ガイドの「Amazon SageMakerをセットアップします」を参照して、AWS アカウントの取得とセットアップを実施してください。

    * `AWS アカウントを作成する <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-set-up.html#gs-account>`_
    * `IAM 管理者ユーザーおよびグループの作成 <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/gs-set-up.html#gs-account-user>`_


ステップ 2 : Amazon SageMaker Studio をセットアップする
-------------------------------------------------------------------

AWS マネジメントコンソールにログインして、リージョンが「東京」であることを確認してください。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-region.jpg
  :width: 900px


AWS マネジメントコンソールの上部の検索ウィンドウに「sagemaker」などと入力して、Amazon SageMaker のコンソールに移動します。

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
| なお、IAM 認証を選択して構築したドメイン (Amazon SageMaker Studio の環境) を AWS SSO 認証に変更する場合は、一度ドメインを削除して再作成が必要になります。特に、本番環境で利用する際は注意して選択してください。

.. list-table::
    :header-rows: 1

    * - セットアップ方法
      - 説明
    * - クイックスタート
      - | ・Amazon SageMaker Studio の操作に必要なアカウントや権限設定の大部分を Amazon SageMaker に任せる
        | ・認証方法は IAM となる
    * - 標準セットアップ
      - | ・Amazon SageMaker Studio の操作に必要なアカウントや権限設定を詳細に行う場合に選択する
        | ・認証方法は IAM に加えて、AWS SSO が選択可能である

| 「クイックスタート」での設定項目やその意味について、下記の表にまとめます。
| なお、今回は検証ですので、特に理由がない限り、以降の設定はデフォルト値のまま進めることとします。

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
        | Amazon SageMaker Pipelines (MLOps プロジェクト) および Amazon SageMaker JumpStart の利用可否を設定する
        | ※ (注意) 上記は AWS Service Catalog や AWS CloudFormation を利用して実装されており、それぞれの利用するリソースへのアクセス許可を設定する
      - 有効


ここで作成される IAM ロールは、`「第5回の連載」 <https://news.mynavi.jp/itsearch/article/devsoft/5099>`_ で作成したものと同様となります。
「AmazonSageMakerFullAccess」と、下記で設定した条件に当てはまる Amazon S3のバケットとオブジェクト(ファイル)の操作権限を持つ IAM ロールが作成されます。
今回は下図の設定で IAM ロールを作成しますが、本番環境で利用する場合は必要最低限の権限を付与してください。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-quick-start-iam-role.jpg
  :width: 600px

「送信」をクリックして、下記に示す「SageMaker Studio コントロールパネル」が表示されればセットアップは完了です。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-control-panel.jpg
  :width: 900px


注意事項
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

著者の AWS アカウントでは、下図のようなエラーが発生しました。
プロジェクトの「起動制約ロール」と「製品使用ロール」が存在しないことを示すエラーです。

.. figure:: ../../../images/blog/10th/amazon-sagemaker-autopilot-tutorial-step2-sagemaker-studio-control-panel-error.jpg
  :width: 900px

しかし、「SageMaker Studio コントロールパネル」の図の赤枠内に示したようにどちらのロールも正常にアタッチされていますので、問題なしと考えて進めることにします。


まとめ
-------------------------------------------------------------------

今回は XXX について解説をしました。次回は YYY について扱いたいと思います。


+++++++++++

.. include:: ../author/author.rst
