Amazon SageMaker の概要
===================================

Amazon SageMaker とは
---------------------------------
- `サービスの紹介ページ <https://aws.amazon.com/jp/sagemaker/>`_ より。

    *Amazon SageMaker は、すべての開発者やデータサイエンティストが機械学習 (ML) モデルを迅速に構築、トレーニング、デプロイできるようにする完全マネージド型サービスです。
    SageMaker は高品質モデルの開発を容易にするため、機械学習の各プロセスから負荷の大きな部分を取り除きます。*

- `開発者ガイド <https://docs.aws.amazon.com/ja_jp/sagemaker/latest/dg/whatis.html>`_ より。

    *Amazon SageMaker は、完全マネージド型の機械学習サービスです。
    Amazon SageMaker では、データサイエンティストと開発者が素早く簡単に機械学習モデルの構築と学習を行うことができ、稼働準備が整ったホスト型環境に直接デプロイできます。
    統合された Jupyter オーサリングノートブックインスタンスから、調査および分析用のデータソースに簡単にアクセスできるため、サーバーを管理する必要がありません。
    また、一般的な機械学習アルゴリズムも使用できます。
    そうしたアルゴリズムは、分散環境できわめて大容量のデータに対しても効率良く実行できるよう最適化されています。
    自前のアルゴリズムやフレームワークもネイティブでサポートされているため、Amazon SageMaker ではお客様固有のワークフローに合わせて調整できる柔軟性の高い分散型トレーニングも行えます。
    Amazon SageMaker コンソールからクリック 1 つで起動して、安全でスケーラブルな環境にモデルをデプロイします。
    トレーニングとホスティングは、分ごとの使用量で課金されます。
    最低料金や前払いの義務はありません。*

提供サービス
----------------------
- `サービスの紹介ページ <https://aws.amazon.com/jp/sagemaker/>`_ より。

.. image:: /../images/amazon_sagemaker_services.png

開発フロー
---------------

開発
^^^^^^^^^^
- ノートブックインスタンスを起動して学習・推論用コードの開発を行う。

学習
^^^^^^^^^
- 学習用インスタンスを起動する。
- Amazon ECR からコンテナイメージをロードする。
- Amazon S3 から学習に必要なデータをダウンロードして学習を行う。
- 学習が完了したら、自動で学習用インスタンスを削除する。
- 学習済モデルとコードを自動で Amazon S3 に保存する。

推論
^^^^^^^^^^
- 推論用インスタンスを起動する。
- Amazon ECR からコンテナイメージをダウンロードする。
- Amazon S3 から学習済モデルとコードをダウンロードし、エンドポイントを作成する。

ビルトインアルゴリズム
-----------------------

画像
^^^^^^^^^
- Image Classification
- Object Detection
- Semantic Segmentation

数値・系列解析
^^^^^^^^^^^^^^^^^^^
- DeepAR Forecasting
- Random Cut Forecast
- IP Insights

自然言語
^^^^^^^^^^^^
- seq2seq
- LDA
- Neural Topic Model
- BlazingText

レコメンデーション
^^^^^^^^^^^^^^^^^^^^^
- Object2Vec
- Factorization Machines

回帰・分類など
^^^^^^^^^^^^^^^^^^^
- Linear Learner
- XGBoost
- k-means
- k-NN
- PCA

ビルトインアルゴリズムの例：Image Classification
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- ResNet (１ILSVRC 2015 優勝モデル) による画像認識
- AWS が学習済モデルを提供しており、転移学習が可能
- 入出力インターフェース

    - 入力

        - イメージ形式 (lst ファイルと画像)

            - lst ファイル

                - 画像のインデックス、分類のインデックス、画像の相対パス

        - RecordIO 形式
        - 拡張マニフェストイメージ形式

    - 出力インターフェース

        - JSON 形式
        - JSON Lines 形式

Amazon SageMaker のサンプルノートブック
----------------------------------------
- GitHub にて`サンプルノートブック<https://github.com/awslabs/amazon-sagemaker-examples>`_が公開されている。
- サンプルノートブックは、Jupyter の "SageMaker Examples"からも選べる。

注意事項
-----------
- Amazon SageMaker の課金対象リソース (利用後に削除しないと課金され続けるもの)

    - ノートブックインスタンス
    - 推論用インスタンス
    - Amazon S3
- ResourceLimitExceeded というエラーが出力された場合

    - 上限緩和申請をする。

MarketPlace
--------------
- モデルの購入が可能

    - 購入者は機械学習で使用するデータを秘匿したままでモデルを使用できる。
- モデルの販売も可能

    - 販売者はモデルの中身を秘匿したままでモデルを販売できる。

参考
----------
- 「今から始める機械学習入門 Amazon SageMaker の初級活用法」
