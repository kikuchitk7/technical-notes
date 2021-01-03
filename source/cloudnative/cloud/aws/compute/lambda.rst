Lambda
==============

前提
-----------
- Python を利用する。


logging
-------------------
- print メソッドを使う
- logging (ログ記録ライブラリ) を使う


logging (ログ記録ライブラリ) を使う
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- `logging <https://docs.python.org/3/library/logging.html>`_ は Python の標準ライブラリ

.. code-block:: python

    import json
    import os

    # logger のインポート 
    import logging
    
    # logger の取得と出力レベルの設定
    logger = logging.getLogger()
    logger.setLevel(logging.INFO)

    def lambda_handler(event, context):
        # INFO レベルで出力
        logger.info('## ENVIRONMENT VARIABLES')
        logger.info(os.environ)
        logger.info('## EVENT')
        logger.info(event)

.. code-block:: python

    START RequestId: 1c8df7d3-xmpl-46da-9778-518e6eca8125 Version: $LATEST
    [INFO]  2020-01-31T22:12:58.534Z    1c8df7d3-xmpl-46da-9778-518e6eca8125    ## ENVIRONMENT VARIABLES

    [INFO]  2020-01-31T22:12:58.534Z    1c8df7d3-xmpl-46da-9778-518e6eca8125    environ({'AWS_LAMBDA_LOG_GROUP_NAME': '/aws/lambda/my-function', 'AWS_LAMBDA_LOG_STREAM_NAME': '2020/01/31/[$LATEST]1bbe51xmplb34a2788dbaa7433b0aa4d', 'AWS_LAMBDA_FUNCTION_NAME': 'my-function', ...})

    [INFO]  2020-01-31T22:12:58.535Z    1c8df7d3-xmpl-46da-9778-518e6eca8125    ## EVENT

    [INFO]  2020-01-31T22:12:58.535Z    1c8df7d3-xmpl-46da-9778-518e6eca8125    {'key': 'value'}

    END RequestId: 1c8df7d3-xmpl-46da-9778-518e6eca8125
    REPORT RequestId: 1c8df7d3-xmpl-46da-9778-518e6eca8125  Duration: 2.75 ms   Billed Duration: 100 ms Memory Size: 128 MB Max Memory Used: 56 MB  Init Duration: 113.51 ms    
    XRAY TraceId: 1-5e34a66a-474xmpl7c2534a87870b4370   SegmentId: 073cxmpl3e442861 Sampled: true   

.. note:: REPORT

    - RequestId: 呼び出しの一意のリクエスト ID。
    - Duration: 関数のハンドラーメソッドがイベントの処理に費やした時間。
    - Billed Duration: 呼び出しの課金対象の時間。
    - Memory Size: 関数に割り当てられたメモリの量。
    - Max Memory Used: 関数によって使用されているメモリの量。
    - Init Duration (初期所要時間): 最初に処理されたリクエストについて、ハンドラーメソッド外で関数をロードしてコードを実行するためにランタイムにかかった時間。
    - XRAY TraceId: トレースされたリクエストの場合、AWS X-Ray のトレース ID。
    - SegmentId: トレースされたリクエストの場合、X-Ray のセグメント ID。
    - Sampled (サンプリング済み): トレースされたリクエストの場合、サンプリング結果。
