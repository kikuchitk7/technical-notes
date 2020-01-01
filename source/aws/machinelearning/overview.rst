Overview
=========================

機械学習における AWS のミッション
--------------------------------
- 「すべての開発者に機械学習の恩恵を」。
- 機械学習を専門に扱う研究者やエンジニアだけでなく、これから機械学習をビジネスに取り入れたいなど機械学習に関する専門的な知識を持たないエンジニアに向けたサービスも提供。

.. image:: ../../../images/機械学習分野におけるAWSのミッション.png

(出典：`機械学習サービスの製品紹介ページ <https://aws.amazon.com/jp/machine-learning/?nc1=h_ls>`_)


機械学習サービスの種類
-------------------------------
- AWS の機械学習サービスの全体像 (ML スタック) を下記に示す。

.. image:: ../../../images/AWSのMLスタック_2019.png

(出典：2019年12月12日開催 「AWS re:Invent 2019 re:Cap | AI/ML」 の資料より)

- 機械学習サービスは、大きく3種類に分けることができる。

    - `AI サービス <https://kikuchitk7.github.io/technical-notes/aws/machinelearning/ai-services/ai-services.html>`_
    - `ML サービス <https://kikuchitk7.github.io/technical-notes/aws/machinelearning/ml-service/ml-service.html>`_
    - ML フレームワークとインフラストラクチャ

- それぞれの機械学習サービスの特徴を下記の表に簡単にまとめる。

.. list-table::
    :widths: 5, 5
    :header-rows: 1

    * - サービス名
      - サービスの特徴
    * - AI サービス
      - * | AWS がユースケースごとに用意したサービスと自身の解決したい問題・課題とマッチする人向けのサービス。
          | (例) 機械翻訳がしたい：Amazon Translate、商品の在庫を予測したい：Amazon Forecast など
        * | 学習済モデル、もしくは、学習モデルを生成する仕組みが AWS から提供される。
          | 利用者が考える部分が少なくて澄むため、導入の難易度は最も低い。
        * | 上記の理由で「機械学習の深い知識なしに利用可能」である。
    * - ML サービス
      - * | 機械学習エンジニアやデベロッパ向けのサービス。
          | Amazon SageMaker とその関連機能により、機械学習のプロセス (開発・学習・デプロイ・推論・監視など)
          | を効率良く行うための機能が提供されている。
        * | AWS が用意したビルトインアルゴリズムを利用して機械学習モデルを開発することや
          | 利用者独自のアルゴリズムを元に機械学習モデルを開発するも可能である。
        * | AI サービスと比較すると柔軟性が増す一方で利用者が準備する部分が増えるため、導入の難易度が上がる。
    * - | ML フレームワークと
        | インフラストラクチャ
      - * | Amazon SageMaker の実行環境をカスタマイズするためのサービス。
        * | 利用者が好きな ML (機械学習) フレームワークを選択して開発することや
          | 機械学習向けに最適化されたインスタンスを利用して、推論を実行することができる。



情報収集方法
-------------------------
著者が情報を得る方法、かつ、無料で得られる情報をまとめる。

- twitter

    - `Amazon Web Services <https://twitter.com/awscloud?s=20>`_
    - `アマゾン ウェブ サービス ジャパン株式会社 <https://twitter.com/awscloud_jp?s=20>`_

- ブログ

    - `AWS Machine Learning Blog <https://aws.amazon.com/jp/blogs/machine-learning/>`_

- 動画

    - `Julien Simon の YouTube チャンネル <https://www.youtube.com/channel/UCVonoXm3SI_Q0ZNHd5JPawA>`_

- セミナー

    - `国内のクラウドセミナー・イベントスケジュール <https://aws.amazon.com/jp/about-aws/events/>`_
    - `AWS サービス別資料 (Black Belt オンラインセミナーの過去資料) <https://aws.amazon.com/jp/aws-jp-introduction/aws-jp-webinar-service-cut/>`_
    - `AWS トレーニングと認定 <https://www.aws.training/>`_
