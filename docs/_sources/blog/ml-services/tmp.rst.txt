| 下記に示すサービスは、昨年末に米国のラスベガスで開催された「AWS re:Invent 2019」で新たに発表された機能です。
| 機能の専用のページが存在するものはリンクを付与しますが、それ以外は `Amazon SageMaker <https://aws.amazon.com/jp/sagemaker/>`_ のページを参照してください。

- Amazon SageMaker Studio (Web ブラウザベースの統合開発環境 (IDE))
- Amazon SageMaker Notebooks (Amazon SageMaker Studio に統合された完全マネージドなノートブック)
- Amazon SageMaker Experiments (学習の実行や結果を一元管理)
- Amazon SageMaker Debugger (学習の監視、記録、分析と異常の検知)
- `Amazon SageMaker Autopilot <https://aws.amazon.com/jp/sagemaker/autopilot/>`_ (AutoML 機能による機械学習モデルの自動構築)
- Amazon SageMaker Model Monitor (本番環境にデプロイした推論エンドポイントの継続的な監視)
- `Amazon Augmented AI (A2I) <https://aws.amazon.com/jp/augmented-ai/>`_ (信頼性の低い推論結果の人間の確認と修正)

| Amazon SageMaker は、登場時から機械学習ワークフローの全体に渡って様々な機能が提供されてきました。
| その後も機能が次々と追加されてきましたが、今回のアップデートでは、より一層の機能が強化されて、更に便利に使うことができるようになりました。

| 特に、Amazon SageMaker Studio の登場により、従来のノートブックインスタンスをメインとする開発スタイルからの変革が起こる可能性があります。
| 開発環境としての機能を提供するだけでなく、Amazon SageMaker の各機能とシームレスに連携することで、機械学習モデルの管理や運用の大部分がここで完結します。

| 例えば、Amazon SageMaker Experiments により、学習の履歴やその際に生成した機械学習モデル、利用した機械学習アルゴリズムやハイパーパラメータ (設定値)、精度と行ったメトリクスの一元管理が可能です。
| 従来は Excel などの別のツールを使って手動管理する必要がありましたが、その手間からの解放だけでなく、記録漏れや精度の良かった機械学習モデルが埋もれて追跡できなくなるなどの問題の発生がなくなります。

図 (Before/After)

| Amazon SageMaker Studio は、2020年4月29日に `一般提供がアナウンス <https://aws.amazon.com/jp/about-aws/whats-new/2020/04/announcing-general-availability-amazon-sagemaker-notebooks/>`_されました。
| 本稿の執筆時点 (2020年5月20日) では東京リージョンでの提供はされていないものの、近い将来的に提供されることが見込まれます。
| 新機能の紹介はこの程度に留めて、後続の連載で詳細に解説したいと思います。


ML サービス の特徴
^^^^^^^^^^^^^^^^^^^^^^^^



ML サービスで提供される機能
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



Amazon SageMaker の利用方法
------------------------------------
| 
機能の関連　ノートブックインスタンスなどの色々な対象が登場する
| ここでは、従来からの Amazon SageMaker の利用方法であるノートブックインスタンスを利用するパターンの解説をベースに整理しながら見ていきましょう。 

| Amazon SageMaker の利用パターンは、実行環境を軸にして下記の2パターンに分けられる。

- ノートブックインスタンスを利用して開発する (従来からの方法)
- Amazon SageMaker Studio を利用して開発する


ノートブックインスタンスを利用して開発する (従来からの方法)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| Amazon SageMaker では、機械学習モデルの開発環境として「ノートブックインスタンス」を構築し、Jupyter Notebook もしくは JupyterLab を使って開発してきました。

| そこで、まずは主要な登場人物を整理します。
| 次に、Amazon SageMaker の実行環境として、ノートブックインスタンスを利用するパターンについて整理します。これが Amazon SageMaker の基本的な利用方法となります。


主要な登場人物の整理
++++++++++++++++++++++++++++++
- `Amazon Simple Storage Service (S3) <https://aws.amazon.com/jp/s3/>`_
- `Amazon Elastic Container Resistry (ECR) <https://aws.amazon.com/jp/ecr/>`_
- Amazon SageMaker

    - ノートブックインスタンス
    - 学習用インスタンス
    - 推論用インスタンス


.. image:: ../../../images/amazon_sagemaker_notebook_instance.png
  :width: 900px


利用の流れ
+++++++++++++++++
機械学習ワークフロー