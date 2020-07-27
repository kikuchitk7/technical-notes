[第8回] Amazon SageMaker の基本的な使い方を理解する (4)
========================================================

はじめに
--------------------
| 前回は、Amazon SageMaker のチュートリアルをベースにして、学習を行って機械学習モデルの構築まで実施しました。
| 今回は学習を行って構築した機械学習モデルを使って、推論を実行してみます。


今回の記事で実施すること
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 今回は下記の表の「ステップ 5: モデルのデプロイ」、「ステップ 6: モデルの性能評価」、「ステップ 7: リソースを終了する」を扱います。
| 4 回に渡って Amazon SageMaker の使い方を解説してきましたが、今回で完了です。


.. list-table::
    :header-rows: 1

    * - 工程
      - ステップ
      - 枝番
      - 実施内容
      - 連載回
    * - 開発
      - 1
      - 
      - Amazon SageMaker コンソールにログインする
      - 第 5 回
    * -
      - 2
      - 
      - Amazon SageMaker notebook instance を作成する
      - 第 5 回
    * -
      - 3
      - 
      - データの準備
      - 第 6 回
    * -
      - 
      - 3a, 3b
      - ノートブックを起動する
      - 
    * -
      - 
      - 3c
      - ノートブックの利用準備をする
      - 
    * -
      - 
      - 3d
      - S3 バケットを作成する
      - 
    * -
      - 
      - 3e
      - 学習・推論に利用するデータをダウンロードする
      - 
    * -
      - 
      - 3f
      - データを分割する
      - 
    * - 学習
      - 4
      - 
      - データからのモデルのトレーニング
      - 第 7 回
    * - 
      - 
      - 4a
      - 学習データを S3 バケットにアップロードする
      - 
    * - 
      - 
      - 4b
      - 学習の設定をする
      - 
    * - 
      - 
      - 4c
      - 学習を行う
      - 
    * - 推論
      - 5
      - 
      - モデルのデプロイ
      - 第 8 回
    * - 
      - 
      - 5a
      - 推論エンドポイントを作成して、モデルをデプロイする
      - 
    * - 
      - 
      - 5b
      - 推論を行う
      - 
    * - 
      - 6
      - 
      - モデルの性能評価
      - 第 8 回
    * - 後片付け
      - 7
      - 
      - リソースを終了する
      - 第 8 回


ステップ 5: モデルのデプロイ
--------------------------------------
| 学習を実行し、機械学習モデルの構築まで完了しました。
| ここでは、機械学習モデルのデプロイと推論データを使って推論 (見込み顧客の予測) を行います。

.. image:: ../../../images/blog/5th/amazon-sagemaker-tutorial-step5.png
  :width: 900px


ステップ 5a: 推論エンドポイントを作成して、モデルをデプロイする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| ここでいう「デプロイ」とは、API サーバの構築を指します。
| 下記に示す一連の処理を deploy メソッドを利用することで実行できます。

- 推論用インスタンスの作成
- S3 バケットからの機械学習モデルのダウンロード
- 推論用コンテナの起動
- API の公開


実行するコード
********************
下記のコードをセルにコピー＆ペーストして実行してください。

.. code-block:: python

  xgb_predictor = xgb.deploy(initial_instance_count=1,instance_type='ml.m4.xlarge')

なお、デプロイが開始されると、下記の画像に示したように処理の進行とともに「- (ハイフン)」が出力されていきます。

.. image:: ../../../images/blog/5th/sagemaker-deploy-start.png
  :width: 900px

デプロイが完了すると、下記のように完了を示す「!」が出力されます。
デプロイ処理は数分で完了します。

.. image:: ../../../images/blog/5th/sagemaker-deploy-complete.png
  :width: 900px


コードの解説
********************

.. code-block:: python

  xgb_predictor = xgb.deploy(
    initial_instance_count=1,
    instance_type='ml.m4.xlarge'
  )

| この処理も改行して記載します。
| Amazon SageMaker SDK for Python の `deploy <https://sagemaker.readthedocs.io/en/stable/api/training/estimators.html#sagemaker.estimator.Estimator.deploy>`_ メソッドを使って、機械学習モデルのデプロイを実行します。
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
        |
        | (参考)
        | - 「`Amazon SageMaker ML インスタンスタイプ <https://aws.amazon.com/jp/sagemaker/pricing/instance-types/>`_」
        | - 「`Amazon SageMaker の料金 <https://aws.amazon.com/jp/sagemaker/pricing/>`_」

| 推論用インスタンスを作成して、S3 バケットから学習済の機械学習モデルを、Amazon ECR から推論用コンテナイメージをダウンロードして、推論用コンテナを起動します。
| アプリなどからアクセスするためのエンドポイントを作成しています。エンドポイントには HTTPS で接続します。

- (備忘) エンドポイントの図をいれる


ステップ 5b: 推論を行う
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


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

| ステップ 3e でデータをダウンロードし、ステップ 3f でそのデータを学習データと推論データ (テストデータ) に分割しました。
| 推論データは `test_data` に格納されていますが、ステップ 3f では単純に分割しただけで、正解 (「y_yes」と「y_no」) が含まれます。
| 顧客の属性情報から見込み顧客であるか否かを予測したいので、正解の値を削除 (drop) しています。

.. code-block:: python

  xgb_predictor.content_type = 'text/csv' # set the data type for an inference
  xgb_predictor.serializer = csv_serializer # set the serializer type

推論データの Content type とシリアライザのタイプに CSV 用のものを指定しています。

.. code-block:: python

  predictions = xgb_predictor.predict(test_data_array).decode('utf-8') # predict!

`predict <https://sagemaker.readthedocs.io/en/stable/api/inference/predictors.html#sagemaker.predictor.RealTimePredictor.predict>`_ メソッドを使って推論データを推論エンドポイントに送信し、推論結果を得ています。

.. code-block:: python

  predictions_array = np.fromstring(predictions[1:], sep=',') # and turn the prediction into an array

推論結果がカンマ区切りのテキストデータで返されるので、後続の精度の評価をするために Numpy の `fromstring <https://numpy.org/doc/1.18/reference/generated/numpy.fromstring.html>`_ メソッドを使って Array に変換しています。


ステップ 6: モデルの性能評価
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| 推論結果を得ることができましたので、最後に精度の評価を行います。
| 二値分類の精度評価においてよく利用される「混同行列 (confusion matrix)」を使います。


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

.. image:: ../../../images/blog/5th/confusion-matrix.png
  :width: 450px

| 行方向 (縦軸) が「正解 (観測; Observed)」、列方向 (横軸) が「予測 (Predicted)」を表します。
| 正解 (2種類) * 予測 (2種類) で、下記に示す 4 つの指標があります。
| 混同行列という名前の通りで混乱しやすいですが、Positive/Negative はあくまで「予測」に対してかかります。
| その予測の正解と不正解により、True/Negative が付いていると考えると理解しやすいと思います。

- True Positive (TP): 定期預金を申し込むと予測して、実際に申し込んだ顧客の数
- False Poritive (FP): 定期預金を申し込むと予測したが、実際には申し込まなかった顧客の数
- True Negative (TN): 定期預金を申し込まないと予測して実際に申し込まなかった顧客の数
- False Negative (FN): 定期預金を申し込まないと予測したが、実際には申し込んだ顧客の数


推論結果についての考察
******************************
上記のコードを実行すると、下記のような出力結果を得られます。

.. image:: ../../../images/blog/5th/sagemaker-predict-confusion-matrix.png
  :width: 450px

| `Overall Classification Rate` で示されている数値が正解率であり、今回は 89.5% でした。
| True Negative が 90% である一方で、True Poritive は 65% とかなり低いように見えます。
| なぜでしょうか？

| 学習データ (train_data) には、28,831 人分の顧客データを利用しました。
| このうち、定期預金を申し込んだ顧客数 (`y_yes` が 1 の合計) と申し込まなかった (`y_no` が 1 の合計) を確認してみます。

.. image:: ../../../images/blog/5th/sagemaker-predict-train-data.png
  :width: 450px

| 定期預金を申し込んだ顧客数が 3,219 人であることに対して、定期預金を申し込まなかった顧客数は 25,612 人と 5 倍近い差があることがわかります。
| **定期預金を申し込んだ顧客のデータ数が不足している可能性** があり、あと 2.2万人程度のデータを用意して再度学習をすると、同程度の精度を出せる可能性があります。

| 二値分類における各ラベルの学習データは、**十分な量を均等に用意すること** が望ましいと言われています。
| 今回は UCI が公開しているオープンデータを利用していますので、データを増やして再検証をすることはできません。
| 実際の業務で同様の問題が発生した場合は、顧客の申し込み履歴がデータベースに格納されているのであれば更にデータを抽出するか、そもそもこれ以上データが存在しない場合は新たにデータを取得して増やさなければなりません。

| また、True Negative が 90% と比較的高い数値を示しているように見えますが、**この数値で十分な精度を確保できているのかは業務要件次第** です。
| 100% に近い数字でなければならない場合は、定期預金を申し込まなかった顧客のデータも増やさなければならない可能性があります。
| **機械学習にかかる時間の約 8 割はデータの準備や前処理である** と言われることがありますが、今回の結果からも垣間見ることができるかと思います。

| あるいは、ステップ 4b で固定値としたハイパーパラメータをチューニングすることで精度を改善できるかもしれません。
| いずれにしても機械学習はこのような仮説立案と検証の繰り返しが必要であり、ビジネスにおいて活用していくには根気や労力が必要です。


ステップ 7: リソースを終了する
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
| ここまでで「開発」「学習」「推論」を一通り実施することができました。
| 課金対象のリソースがありますので、課金を防ぐためにリソースを削除します。

| 課金される可能性があるリソースは、下記の表の通りとなります。
| チュートリアルのステップ 7 の手順では「推論エンドポイント」と「S3 バケットに格納されているオブジェクト」しかありませんので、その他のリソースの削除漏れに注意してください。
| **請求ダッシュボードや Cost Explorer で課金されていないことを必ず確認してください。**

.. list-table::
    :header-rows: 1

    * - サービス名
      - リソース名
    * - Amazon SageMaker
      - ノートブックインスタンス
    * -
      - 推論エンドポイント (推論用インスタンス・推論用コンテナ)
    * - Amazon S3
      - S3 バケットに格納されているオブジェクト
    * - Amazon CloudWatch
      - CloudWatch Logs に格納されているログ


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
上記の通り、「ノートブックインスタンス」と「CloudWatch Logs」も課金対象となりますので、削除漏れに注意してください。

ノートブックインスタンスは「停止」と「削除」の 2 段階での対応が必要です。

| まず、ノートブックインスタンスの削除を行います。
| JupyterLab のタブは、Web ブラウザの「×」ボタンなどで閉じてください。

| AWS マネジメントコンソールのノートブックインスタンスの画面に移動します。
| 削除対象のノートブックインスタンスをラジオボタンで選択し、「アクション」で「停止」を選択します。
| 停止処理に移ると、「ステータス」が「InService」から「Stopping」に移行しますので、数分待ちます。

.. image:: ../../../images/blog/5th/sagemaker-notebook-delete-1.png
  :width: 900px


「ステータス」が「Stopped」になったことを確認して、「アクション」で「削除」をクリックします。

.. image:: ../../../images/blog/5th/sagemaker-notebook-delete-2.png
  :width: 900px


ノートブックインスタンスの削除を確認するウィンドウが表示されますので、「削除」をクリックします。

.. image:: ../../../images/blog/5th/sagemaker-notebook-delete-3.png
  :width: 450px


| 「ステータス」が「Deleting」に移行し、数分待つとノートブックインスタンスが画面からなくなります。
| これでノートブックインスタンスの削除は完了です。


| 次に、CloudWatch Logs の削除を行います。
| AWS マネジメントコンソールのトップ画面などから「CloudWatch」を検索して移動します。

.. image:: ../../../images/blog/5th/cloudwatch-logs-delete-1.png
  :width: 900px


CloudWatch の画面に移動できたら、左側のメニューから「ロググループ」を選択します。

.. image:: ../../../images/blog/5th/cloudwatch-logs-delete-2.png
  :width: 450px


| ロググループの画面に移動できたら、検索バーに「sagemaker」と入力してログを検索します。
| 削除するログを全て選択して、「アクション」で「削除」をクリックします。

.. image:: ../../../images/blog/5th/cloudwatch-logs-delete-3.png
  :width: 900px


ロググループの削除を確認するウィンドウが表示されますので、「削除」をクリックします。

.. image:: ../../../images/blog/5th/cloudwatch-logs-delete-4.png
  :width: 450px


ロググループが画面からなくなったら削除は完了です。

| S3 バケットと IAM ロールも作成しましたが、これらは存在するだけでは課金されません。
| それぞれのサービスの画面から必要に応じて削除してください。


まとめ
--------------------
| 今回までの数回の記事では、Amazon SageMaker を実際に使って開発・学習・推論をする方法についてご説明させていただきました。
| 次回は、Amazon SageMaker Studio の使い方についてみていきたいと思います。


+++++++++++

.. include:: ../author/author.rst
