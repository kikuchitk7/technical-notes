Amazon Forecast
====================

概要
---------
- 過去の履歴から将来を予測する時系列データ予測サービス
- Forecast の提供範囲 (「 `Black Belt 資料 <https://www.slideshare.net/AmazonWebServicesJapan/20200128-aws-black-belt-online-seminar-amazon-forecast>`_ の P.13 より)

    - データの読み込み
    - データの解析
    - 特徴判定
    - アルゴリズム選択
    - ハイパーパラメータの選択
    - モデル学習
    - モデル最適化
    - モデルのホスト

- Amazon.com と同様の技術で、様々なドメインに対応可能で、長期的な予測を行うことができる
- 機械学習モデルの構築を大幅に簡素化するために、一連の事前定義済アルゴリズムに加えて、モデルをトレーニングするための AutoML オプションを提供

ユースケース
------------------
- Forecast の「 `開発者ガイド 「Amazon Forecast とは」 <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/what-is-forecast.html>`_ 」より。
.. 

    - 小売製品の需要 (ウェブサイト上または特定の店舗やロケーションで販売されている製品に対する需要など)
    - サプライチェーンの需要 (製造に必要な原材料、サービス、またはその他の投入量を含む)
    - リソース要件 (コールセンターのエージェント数、契約社員、IT スタッフ、需要を満たすのに必要なエネルギーなど)
    - 運用上のメトリクス (サーバーへのウェブトラフィック、AWS の使用状況、IoT センサーの使用状況など)
    - ビジネス上のメトリクス (地域ごとまたはサービスごとのキャッシュフロー、売上、利益、および経費など)

- ユースケースはデータセットドメインと同義。
- Forecast のデータセットドメイン。 ( `開発者ガイド 「事前定義済みのデータセットドメインとデータセットタイプ」 <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/howitworks-domains-ds-types.html>`_ より)

.. list-table:: 
    :widths: 5, 5
    :header-rows: 1

    * - RETAIL ドメイン
      - 小売の需要予測
    * - INVENTORY_PLANNNING ドメイン
      - サプライチェーンとインベントリの計画
    * - EC2 CAPACITY ドメイン
      - Amazon Elastic Compute Cloud (Amazon EC2) キャパシティの予測
    * - WORK_FORCE ドメイン
      - 従業員の計画
    * - WEB_TRAFFIC ドメイン
      - 今後のウェブトラフィックの見積もり
    * - METRICS ドメイン
      - 収益およびキャッシュフローなどの予測メトリクス
    * - CUSTOM ドメイン
      - その他すべての時系列予測のタイプ

Forecast における AutoML 機能
-------------------------------------
- Forecast の「 `開発者ガイド 「Amazon Forecast とは」 <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/what-is-forecast.html>`_ 」より。
.. 

    Amazon Forecast は、機械学習モデルの構築を大幅に簡素化します。
    Forecast には、一連の事前定義済みアルゴリズムに加えて、モデルをトレーニングするための AutoML オプションが用意されています。
    AutoML は、アルゴリズムの選択、ハイパーパラメータの調整、反復モデリング、モデル評価などの複雑な機械学習タスクを自動化します。
    開発者に機械学習の専門知識がない場合でも、Amazon Forecast API、AWS Command Line Interface (AWS CLI)、または Amazon Forecast コンソールを使用して、
    トレーニングデータを 1 つ以上の Amazon Forecast データセットにインポートして、予測子をトレーニングし、予測を生成することができます。

- Predictor (予測子) の作成時に時系列予測アルゴリズムの選択を行う。ここで AutoML 機能の利用を選択できる。

利用の流れ
-------------------
- データの準備

    - 学習データは **CSV 形式** で準備する。
    - 学習データは「 `Forecast のデータセットのガイドライン <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/dataset-import-guidelines-troubleshooting.html>`_ 」に従って準備する必要がある。
    - 学習データは S3 に格納する。

- データセットタイプとスキーマ

    - 下記の3種類のデータセットタイプをインポート可能

        .. list-table:: 
            :widths: 5, 5, 5
            :header-rows: 1

            * - データセットタイプ
              - 説明
              - 例
            * - Target time series (必須)
              - 予測したいデータに関する過去のデータ (時系列データ)
              - 売り上げデータなど
            * - Related time series
              - 予測したいデータに関連する時系列データ
              - キャンペーンデータ
            * - Item meta data
              - 予測したいデータに関連する静的なデータ
              - 商品カテゴリ

    - スキーマ

        - データセットの構造をスキーマ (JSON形式) で定義する必要がある。

- 前処理

    - **Target time series のみ、学習の前に前処理をすることができる。**
    
        - 下記を実行可能。

            - 時間間隔の調整
            - 欠損値の補完

- Predictor (予測子) の作成

    - Predictor は学習モデルのことであり、Predictor の作成は学習を指す。
    - アルゴリズムは下記の5種類から選択可能。

        - ARIMA (事故回帰和分移動平均)
        - ETS (指数平滑法)
        - NPTS (ノンパラメトリック時系列)
        - Prophet (局所的なベイズ構造時系列モデル)
        - DeepAR+ (RNN)
    
    - アルゴリズムによって必要となるデータセットタイプが異なる。
    - DeepAR+ のみハイパーパラメータ最適化 (HPO) をサポートしている。
    - 学習データを分割して、評価データにすることができる。
    - AutoML 機能はここで設定する。(`開発者ガイド 「予測子の作成」 <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/howitworks-predictor.html#howitworks-predictor-intro>`_ より)

    .. 

        AutoML の実行 – Amazon Forecast には、事前に定義されたアルゴリズムが用意されています。
        どのアルゴリズムを選ぶべきかわからない場合は、PerformAutoML オプションを使用してください。
        このオプションでは、すべてのアルゴリズムを評価し、データセットに基づいて最適なアルゴリズムを選択するよう Amazon Forecast に指示します。
        このオプションを使用すると、モデルのトレーニングに時間がかかることがありますが、正しいアルゴリズムおよびパラメータの選択を懸念する必要がありません。
        AutoML は、重み付けされた P10、P50、および P90 分位損失の平均を最適化し、最小値のアルゴリズムを返します。

- Forecast の作成

    - 予測結果を取得するために使用するエンドポイント

料金体系と提供リージョン
-----------------------------
- TBW
- 東京リージョンで利用可能

参考
------------
- 「 `Amazon Forecast 開発者ガイド <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/what-is-forecast.html>`_
