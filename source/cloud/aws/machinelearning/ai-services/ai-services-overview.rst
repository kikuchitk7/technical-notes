AI サービスの概要
=============================

AI サービスとは
---------------------
- AWS の機械学習サービススタックのうち、1番上位に位置するもの。
- 利用者が **データを用意するだけ** で、API から機械学習を利用できる。
- 利用する機械学習は、AWS によって最適な実装がされている。
- 2019年10月時点で10種類の AI サービスを提供している。

AutoML 機能があるサービス (カスタムモデルが構築可能なサービス)
-----------------------------------------------------------------
- Amazon Rekognition

   - `Custom Labels <https://docs.aws.amazon.com/rekognition/latest/customlabels-dg/what-is.html>`_

      No machine learning expertise is required to build your custom model. 
      Amazon Rekognition Custom Labels includes automated machine learning (AutoML) capabilities that take care of the machine learning for you. 
      When the training images are provided, Amazon Rekognition Custom Labels can automatically load and inspect the data, 
      select the right machine learning algorithms, train a model, and provide model performance metrics.

- Amazon Comprehend

   - `Comprehend Custom <https://docs.aws.amazon.com/ja_jp/comprehend/latest/dg/auto-ml.html>`_

      Comprehend custom helps you meet your specific needs even if you don't have the skillset to build machine learning-based NLP solutions. 
      Using automatic machine learning, or AutoML, Comprehend Custom builds customized NLP models on your behalf, using data you already have. 
      Training and calling custom comprehend models are both async (batch) operations.

      - `Custom Classification <https://docs.aws.amazon.com/ja_jp/comprehend/latest/dg/how-document-classification.html>`_
      - `Custom Entity Recognition <https://docs.aws.amazon.com/ja_jp/comprehend/latest/dg/custom-entity-recognition.html>`_

- `Amazon Forecast <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/howitworks-predictor.html#howitworks-predictor-intro>`_

   AutoML の実行 – Amazon Forecast には、事前に定義されたアルゴリズムが用意されています。
   どのアルゴリズムを選ぶべきかわからない場合は、PerformAutoML オプションを使用してください。
   このオプションでは、すべてのアルゴリズムを評価し、データセットに基づいて最適なアルゴリズムを選択するよう Amazon Forecast に指示します。
   このオプションを使用すると、モデルのトレーニングに時間がかかることがありますが、正しいアルゴリズムおよびパラメータの選択を懸念する必要がありません。
   AutoML は、重み付けされた P10、P50、および P90 分位損失の平均を最適化し、最小値のアルゴリズムを返します。

- `Amazon Personalize <https://docs.aws.amazon.com/ja_jp/personalize/latest/dg/working-with-predefined-recipes.html>`_

   Amazon Personalize では、入力データの分析に基づいて、最適な階層的再帰型ニューラルネットワーク (HRNN) レシピが自動的に選択されるようにすることが可能です。
   このオプションは AutoML と呼ばれます。AutoML を実行するには、CreateSolution API を呼び出すときに performAutoML パラメータを true に設定します。
   または、達成する目標とレシピの習熟度に基づいて、特定のレシピを選択できます。各レシピはそれぞれ特定のユースケース向けに設計されています。
   ソリューションを作成するときに、ニーズに最適なレシピを選択します。

- Amazon Fraud Detector

   - プレビューなので正確にはわからないが、過去の不正データを S3 にアップロードしてカスタムモデルを作成する仕組みなので、AutoML 機能ありと判断。
   - `FAQ <https://aws.amazon.com/jp/fraud-detector/faqs/>`_ に下記の記載がある。

   まず、機械学習モデルテンプレートを選択します。
   これは、特定のオンライン不正を検出するために最適化された機能とアルゴリズムの組み合わせを指定します。
   次に、過去の不正データをアップロードして、独自の情報に基づいてカスタマイズされた不正検出モデルを自動的にトレーニング、テスト、デプロイします。

AutoML 機能がないサービス (上記以外すべて)
-----------------------------------------------
- Amazon Polly (「 `カスタム辞書 <https://aws.amazon.com/jp/polly/features/?nc=sn&loc=3>`_ 」によるチューニングは可能)
- Amazon Lex
- Amazon Textract
- Amazon Transcribe (「 `カスタム語彙 <https://docs.aws.amazon.com/ja_jp/transcribe/latest/dg/how-vocabulary.html>`_ 」によるチューニングは可能)
- Amazon Translate (「 `カスタム用語 <https://docs.aws.amazon.com/ja_jp/translate/latest/dg/how-custom-terminology.html>`_ 」によるチューニングは可能)
- Amazon CodeGuru (プレビューなので正確にはわからない)
- Amazon Kendra (`製品ページ <https://aws.amazon.com/jp/kendra/features/>`_ によると「独自シノニム」や「増分学習」によるチューニングや機能拡張は可能となる予定)
- Contact Lens for Amazon Connect (「 `カスタムボキャブラリー <https://aws.amazon.com/jp/connect/contact-lens/features/>`_ 」によるチューニングは可能となる予定)
