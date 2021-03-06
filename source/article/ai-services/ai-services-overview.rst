[第3回] AI サービスの全体像を把握する
==============================================

はじめに
----------------
| `前回 <https://news.mynavi.jp/itsearch/article/cloud/4799>`_ は、AWS の機械学習サービスの全体像と機械学習を活用する際の前提事項ついてご説明しました。
| 今回は `AI サービス <https://aws.amazon.com/jp/machine-learning/ai-services/>`_ の全体像を見ていきたいと思います。

今回の記事では、下記を理解していただくことを目標に解説をしていきます。

- AI サービスのラインナップとそれぞれの概要 (特長、ユースケース、東京リージョンでの利用可否等) がわかる。

なお、今回の記事では全体感をつかむことを目標とし、個々の AI サービスの詳細や具体的な使い方については後続の連載で扱う予定です。

AI サービスとは
---------------------

AI サービスの全体像
^^^^^^^^^^^^^^^^^^^^^^^^^
| AI サービスは、3つのレイヤーで提供される機械学習サービスで最上位に位置するサービスであり、下図の赤枠内のサービスです。
| AWS re:Invent 2019 で下記の4つのサービスが新たに追加され、AI サービスは 10 カテゴリー、13 サービスとなりました。
| ただし、これらは2020年3月24日時点で「プレビュー」での提供であり、東京リージョンでは提供されていません。一般公開までに仕様が変更される可能性がありますので、本番環境でご利用される場合はご注意ください。

- Amazon Kendra (ドキュメント検索サービス)
- Amazon CodeGuru (コードレビューの自動化サービス)
- Amazon Faud Detector (不正検知サービス) ※ 要プレビュー参加申し込み
- Contact Lens for Amazon Connect (Amazon Connect によるコンタクトセンターの分析サービス) ※ 要プレビュー参加申し込み

.. image:: ../../../images/aws_ml_stack_2019_ai.png
  :width: 900px

(出典：2019年12月12日開催 「AWS re:Invent 2019 re:Cap | AI/ML」 の資料より)


AI サービスの特徴
^^^^^^^^^^^^^^^^^^^^^^^^^^
AI サービスの主な特徴は、下記の2点です。

#. AWS から学習済の機械学習モデル、もしくは、機械学習モデルを生成する仕組みが提供される。
#. ユースケースごとにサービスが用意されている。

それぞれの特徴について、詳しく見ていきます。


AWS から学習済の機械学習モデル、もしくは、機械学習モデルを生成する仕組みが提供される。
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
AI サービスでは、AWS から下記のいずれかが提供されます。それぞれを「パターン①」「パターン②」とします。

- パターン①：学習済の機械学習モデル
- パターン②：機械学習モデルを自動で生成する仕組み (AutoML 機能)

Amazon Translate のようにパターン①しかないサービスもあれば、Amazon Rekognition のように機能によってパターン①とパターン②の両方の機能を備えるサービスもある点に注意してください。

| このパターン分けは、AutoML 機能の有無と捉えることもできます。
| AutoML とは、"Automated Machine Learning" の略であり、ユーザが準備した学習データからユーザ独自の機械学習モデルを自動で構築することができます。
| AIサービスによって自動化範囲が多少異なりますが、多くが機械学習のワークフローのうちの「モデルの構築 (機械学習アルゴリズムの選定を含む)」「モデルの学習」「モデルの評価」のプロセスを自動化します。
| この記事では、各 AI サービスの開発者ガイドで "AutoML" と記載があるか、同等の機能を有するものを「AutoML 機能あり」と考えます。

パターン①：学習済の機械学習モデル
***************************************
| このパターンでは、AWS が準備した機械学習モデルを利用します。すなわち、学習データの準備や機械学習アルゴリズムの選定は AWS が行い、それらを基にして構築された機械学習モデルをユーザが利用する形になります。
| AWS の AI サービスの多くのサービス・機能がこのパターンとなります。

| 例えば、Amazon Translate を考えてみましょう。
| Amazon Translate を使うと、「テキストの翻訳」を行うことができます。英語のテキストから日本語のテキストへの翻訳やその逆も行うことができます。
| 下図は Amazon Translate のコンソールで「`Amazon Translate の開発者ガイド <https://docs.aws.amazon.com/translate/latest/dg/what-is.html>`_ 」の冒頭のテキスト (英語) の日本語訳を行ったものです。

.. image:: ../../../images/amazon_translate_example.png
  :width: 900px

| この時にユーザがやるべきことは、「翻訳したいテキストの準備」と「Amazon Translate での翻訳」だけです。
| 学習データとなる英語や日本語のテキストの準備や機械学習アルゴリズムの選定、機械学習モデルの構築・デプロイは不要です。
| 今回はコンソールで翻訳しましたが、Amazon Translate の SDK を利用してユーザのアプリに翻訳機能を組み込むことも可能です。

| AI サービスの謳い文句となっている「機械学習の深い知識なしに利用可能」や「素早くビジネスへの取り込みが可能」というメリットがある反面、機械学習モデルに関してチューニングできる範囲が限定されるというデメリットがあります。
| 例えば、Amazon Translate では「`カスタム用語 <https://docs.aws.amazon.com/ja_jp/translate/latest/dg/how-custom-terminology.html>`_」が提供されています。
| テキスト中にユーザの組織に固有の単語が存在する場合は翻訳に失敗することが想定されますが、カスタム用語として単語を登録することで正しく翻訳させることができます。
| このようなチューニングを行ってもビジネス要件 (精度等) を満たせない、もっときめ細やかなチューニングを行いたいという場合は、Amazon SageMaker を利用するなど独自の機械学習モデルを構築する必要があります。


パターン②：機械学習モデルを自動で生成する仕組み (AutoML 機能)
******************************************************************
| このパターンでポイントとなるのが、機械学習モデルを自動生成する仕組みである「AutoML 機能」です。
| 下記の AI サービスでは AutoML 機能が提供されています。
| なお、Amazon Fraud Detector はユーザの過去の不正データから不正検知モデルを自動生成するサービスです。`Amazon Fraud Detector の開発者ガイド <https://docs.aws.amazon.com/frauddetector/latest/ug/what-is-frauddetector.html>`_ に "AutoML" という単語が登場しないものの、同等の機能を持つため「AutoML 機能あり」と考えてパターン②に分類しています。

.. list-table::
  :header-rows: 1

  * - サービス名
    - 機能名
    - 機能の説明
  * - Amazon Rekognition
    - `カスタムラベル <https://docs.aws.amazon.com/ja_jp/rekognition/latest/customlabels-dg/what-is.html>`_
    - | ユーザ独自のラベルを定義して画像から検出できる。
      | 例：ペットの犬のラベルに定義して、画像から検出する。
  * - Amazon Comprehend
    - `カスタムエンティティ <https://docs.aws.amazon.com/ja_jp/comprehend/latest/dg/custom-entity-recognition.html>`_
    - | ユーザ独自のエンティティ (人、組織、場所など) を定義し、
      | テキストから抽出することができる。
      | 例：組織に固有なポリシー番号などを定義してテキストから抽出する。
  * -
    - `カスタム分類子 <https://docs.aws.amazon.com/ja_jp/comprehend/latest/dg/how-document-classification.html>`_
    - | ユーザ独自のラベルを定義し、テキストを分類するモデルを作成できる。
      | 例：顧客からのメール問合せの内容から担当部署に振り分ける。
  * - Amazon Personalize
    - `レコメンドモデルの自動構築 <https://docs.aws.amazon.com/ja_jp/personalize/latest/dg/working-with-predefined-recipes.html>`_
    - | 最適なレコメンドアルゴリズムを自動選択して、
      | ユーザ独自のモデルを構築できる。
  * - Amazon Forecast
    - `時系列データ予測モデルの自動構築 <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/howitworks-predictor.html#howitworks-predictor-intro>`_
    - | 最適な時系列予測アルゴリズムを自動選択して、
      | ユーザ独自のモデルを構築できる。
  * - Amazon Fraud Detector
    - `不正検知モデルの自動構築 <https://docs.aws.amazon.com/frauddetector/latest/ug/what-is-frauddetector.html>`_
    - | 過去の不正データからユーザ独自の不正検知モデルを
      | 自動構築できる。

| パターン②のメリットは、AutoML 機能を使ってユーザのデータに即した機械学習モデルを構築することができることです。
| 実際にユーザのビジネスにおいて生成されたデータに基づいて機械学習モデルを構築できるため、より確からしい予測ができることが期待できます。
| 単純に利用するだけのパターン①と比べると、機械学習モデルの構築・学習・評価のプロセスが増える分だけ時間がかかりますが、この部分は AutoML 機能が自動で行ってくれます。
| Amazon SageMaker 等を使ってゼロから構築する場合と比べると迅速にビジネスに機械学習を導入することができます。

| ここでは、Amazon Forecast における AutoML 機能をみてみましょう。
| Amazon Forecast では、下記の5つの時系列予測アルゴリズムを利用することができます。

- `ARIMA (自己回帰和分移動平均) <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/aws-forecast-recipe-arima.html>`_
- `DeepAR+ <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/aws-forecast-recipe-deeparplus.html>`_
- `ETS (指数平滑法) <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/aws-forecast-recipe-ets.html>`_
- `NPTS (ノンパラメトリック時系列) <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/aws-forecast-recipe-npts.html>`_
- `Prophet <https://docs.aws.amazon.com/ja_jp/forecast/latest/dg/aws-forecast-recipe-prophet.html>`_

| 取り扱うデータやそれぞれのアルゴリズムの特徴を考慮した上でアルゴリズムを選択すべきですが、それには機械学習に関する専門的な知識が必要となります。
| Amazon Forecast の AutoML 機能を利用すると、上記の5つのアルゴリズムから最適なアルゴリズムを自動で選択することができます。
| Amazon Forecast の AutoML 機能は、下記を自動で行います。

- 上記の5つのアルゴリズムを使ってそれぞれ学習を行い、時系列予測の機械学習モデルを構築する。
- 時系列予測の機械学習モデルの精度の評価を行う。
- 最も精度の高い機械学習モデルを選択し、ユーザに提示する。

Amazon Forecast のコンソールで AutoML 機能を利用する場合についてみてみましょう。

.. image:: ../../../images/amazon_forecast_algorithm_selection.png

| AutoML 機能は、予測子 (Predictor) の設定の際に、「Algorithm selection (アルゴリズムの選択)」で "Automatic (AutoML)" を選択するだけで利用できます。
| 「予測子」とは、「時系列予測の機械学習モデル」のことであり、「予測子の作成」は「機械学習モデルの構築」を意味します。です。作成ボタン押下後、しばらく待つと予測子が作成されます。

.. image:: ../../../images/amazon_forecast_predictor_overview.png
  :width: 900px

| 上記が最も精度が高かった時系列予測の機械学習モデルの提示画面です。
| この場合は、DeepAR+ という再帰型ニューラルネットワーク (Recurrent Neural Network) を用いたアルゴリズムが選択されました。
| これを本番環境にデプロイして、将来の時系列データに対する予測を行うことができます。

.. image:: ../../../images/amazon_forecast_algorithm_metrics_arima.png
  :width: 900px

| Amazon Forecast のコンソールでは、その他の4つの機械学習モデルの情報を確認することもできます。
| 上記は ARIMA を使った機械学習モデルの情報であり、赤枠のプルダウンから選択することで他の機械学習モデルの情報を確認することができます。

| AI サービスの提供形態を整理しつつ、AI サービスが簡単に利用できることをみてきました。

| このパートのまとめとして、パターン①とパターン②の機械学習のワークフローにおけるカバー範囲を下記に示します。
| 参考情報として、ML サービス (Amazon SageMaker) にて独自モデルを構築する場合についても示します。
| 利用するサービスや機能によって多少の違いがある点に注意してください。

.. image:: ../../../images/ml_workflow.png
  :width: 900px

(※ 機械学習のワークフローは、`「機械学習のワークフローってどうなっているの ? AWS の機械学習サービスをグラレコで解説 (builders.flash)」 <https://aws.amazon.com/jp/builders-flash/202003/awsgeek-sagemaker/>`_」を参考に作成)


ユースケースごとにサービスが用意されている。
++++++++++++++++++++++++++++++++++++++++++++++++++++
ここからは、それぞれの AI サービスのユースケースについて整理します。

| Amazon Translate のように単一の機能 (テキストの翻訳) が提供されているものがある一方で、Amazon Rekognition のようにたくさんの機能が提供されているものもあります。
| ここでは表形式で簡単にサービスの説明や代表的なユースケースなどの概要を紹介するに留め、次回以降の記事で詳細な説明や具体的な利用方法について扱うこととします。

| 下記にまとめる情報は、2020年3月24日時点の公開情報に基づいて作成します。
| 「カテゴリー」や「サービス」の記載は文献によって多少の違いがありますが、ここでは上記で示した「AWS の ML スタック」に従って記載することとします。
| また、読者の多くは東京リージョンでの利用を想定していると考えられるため、東京リージョンでの利用可否についても記載します。
| 言語を扱うサービスでは日本語の対応状況がポイントとなると考えられるため、カッコ内に日本語の対応状況を記載します。

.. list-table::
    :header-rows: 1

    * - カテゴリー
      - サービス名
      - サービスの説明と代表的なユースケース
      - AutoML 機能の有無
      - 東京での利用可否
    * - | Vision
        | 画像・動画像処理
      - `Amazon Rekognition <https://aws.amazon.com/jp/rekognition/>`_
      - | **画像・動画像処理**
        | - 事前定義のラベルによる人物や物体の特定
        | - カスタムラベルによる人物や物体の特定
        | - 有害コンテンツの特定
        | - テキストの検出
        | - 顔の検出と性別や年齢、感情の分析
        | - 顔の検索と人物特定
        | - 画像・動画像内の物体やシーンの特定
        | - 画像・動画像内の人物の表情や感情の分析
      - | ○
        | - カスタムラベル
      - ○
    * - | Speech
        | 音声認識
      - `Amazon Polly <https://aws.amazon.com/jp/polly/>`_
      - | **テキストの読み上げ**
        | - Web サイトなどの文書コンテンツの読み上げ
        | - コンタクトセンター業務の定型文章の読み上げ
      - ×
      - | ○
        | (日本語対応済)
    * - 
      - `Amazon Transcribe <https://aws.amazon.com/jp/transcribe/>`_
      - | **音声の文字起こし**
        | - 音声ファイルの文字起こし
        | - 複数の話者 (2~10人) が存在する会議等の音声ファイルの文字起こし
        | - チャネル (話者) ごとの文字起こし
        | - 個人情報等の自動マスキング
        | - ストリーミング音声のリアルタイム文字起こし ※ 日本語未対応
        | - 医療に特化した文字起こし (`Amazon Transcribe Medical <https://aws.amazon.com/jp/transcribe/medical/>`_) ※ 日本語未対応
      - ×
      - | △
        | (日本語未対応機能あり)
    * - | Text
        | 自然言語処理
      - `Amazon Comprehend <https://aws.amazon.com/jp/comprehend/>`_
      - | **自然言語処理・テキスト分析**
        | - テキスト (メール、SNS、顧客アンケート結果など) からのキーフレーズ抽出、感情分析、構文解析、エンティティ認識、言語検出、トピック分類
        | - カスタムエンティティによるユーザ独自のエンティティ (人、組織、場所など) の分類
        | - カスタム分類子によるユーザ独自のラベルの定義とテキストの分類
        | - 医療に特化したテキスト分析 (`Amazon Comprehend Medical <https://aws.amazon.com/jp/comprehend/medical/>`_) ※ 日本語未対応
      - | ○
        | - カスタムエンティティ
        | - カスタム分類子
      - | △
        | (日本語未対応機能あり)
    * - 
      - `Amazon Translate <https://aws.amazon.com/jp/translate/>`_
      - | **テキストの翻訳**
        | - 大量テキスト (業務マニュアル等) のバッチ翻訳 (一括翻訳)
        | - テキストのリアルタイム翻訳
      - ×
      - | ○
        | (日本語対応済)
    * - 
      - `Amazon Textract <https://aws.amazon.com/jp/textract/>`_
      - | **ドキュメント分析**
        | - 画像データからのテキスト、フォーム (Key-Value ペア)、表の抽出
        | - 抽出したデータの Amazon S3、Amazon DynamoDB、Amazon Elasticsearch Service、Amazon Comprehend への格納
      - ×
      - | ×
        | (日本語未対応)
    * - | Search
        | ドキュメント検索
      - `Amazon Kendra <https://aws.amazon.com/jp/kendra/>`_ \*1
      - | **検索サービス**
        | - 様々なデータソース (ファイルシステム、RDB、S3 など) に対する検索機能
      - ×
      - | ×
        | (日本語未対応)
    * - | Chatbot
        | チャットボット
      - `Amazon Lex <https://aws.amazon.com/jp/lex/>`_
      - | **チャットボット**
        | - 顧客の音声もしくはテキストによる問合せや FAQ の対応
      - ×
      - | ×
        | (日本語未対応)
    * - | Personalization
        | レコメンド
      - `Amazon Personalize <https://aws.amazon.com/jp/personalize/>`_
      - | **レコメンド**
        | - 顧客の購買履歴や購買行動に基づいた商品や類似商品のレコメンド
        | - おすすめ順 (ランキング) への並べ替え
      - | ○
        | - レコメンドモデルの自動構築
      - ○
    * - | Forecasting
        | 予測
      - `Amazon Forecast <https://aws.amazon.com/jp/forecast/>`_
      - | **時系列データの予測**
        | - 小売の需要予測
        | - サプライチェーンとインベントリの計画
        | - 従業員の計画
        | - 今後のウェブトラフィックの見積もり
        | - 収益およびキャッシュフローなどの予測メトリクス
        | - その他の任意の時系列データの予測
      - | ○
        | - 時系列データ予測モデルの自動構築
      - ○
    * - | Fraud
        | 不正検知
      - `Amazon Fraud Detector <https://aws.amazon.com/jp/fraud-detector/>`_ \*1
      - | **不正検知**
        | - 過去の不正データを基にしたユーザ独自の不正検知モデルの自動構築
      - | ○
        | - 不正検知モデルの自動構築
      - ×
    * - | Development
        | 開発ツール
      - `Amazon CodeGuru <https://aws.amazon.com/jp/codeguru/>`_ \*1
      - | **コードレビューの自動化**
        | - ソースコードのレビュー (問題の検出と修正方法の提示) の自動化 (`Amazon CodeGuru Reviewer <https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/welcome.html>`_)
        | - アプリのパフォーマンスの分析とパフォーマンス最適化方法の提示 (`Amazon CodeGuru Profiler <https://docs.aws.amazon.com/codeguru/latest/profiler-ug/what-is-codeguru-profiler.html>`_)
      - ×
      - ×
    * - | Contact Center
        | コンタクトセンター
      - `Contact Lens for Amazon Connect <https://aws.amazon.com/jp/connect/contact-lens/>`_ \*1
      - | **Amazon Connect のコンタクトセンター分析**
        | - 顧客の連絡先ページでの全文検索
        | - 顧客とのやり取りの文字起こしデータを基にした感情分析
        | - 顧客の感情の推移などの詳細情報の提示
        | - 顧客の連絡先の自動分類
      - ×
      - ×

- \*1: 2020年3月24日時点でプレビューでの提供。


まとめ
----------------
今回の記事では、AI サービスの全体像についてご説明させていただきました。
次回は、ML サービスの概要について見ていきたいと思います。

+++++++++++

.. include:: ../author/author.rst
