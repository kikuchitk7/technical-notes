Alexa
===================================================


学習リソース
---------------------------------------------------

* `Alexa公式 動画シリーズ「Alexa道場」 <https://developer.amazon.com/ja-JP/alexa/alexa-skills-kit/get-deeper/webinars>`_
* `GitHub: alexa/skill-sample-nodejs-coffeeshop <https://github.com/alexa/skill-sample-nodejs-coffeeshop>`_


用語
---------------------------------------------------

* :ref:`Alexa スキル <alexaskill>`
* :ref:`呼び出し名 <invocationname>`
* :ref:`対話モデル <interactionmodel>`
* :ref:`インテント <intent>`
* :ref:`サンプル発話 <utterance>`

参考：`Alexa Skills Kit 用語集 <https://developer.amazon.com/ja-JP/docs/alexa/ask-overviews/alexa-skills-kit-glossary.html>`_


.. _alexaskill:

Alexa スキル
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Alexa の機能を拡張するアプリケーションプログラム。
* Echo デバイスはデフォルト状態でもニュースの読み上げなど色々できるが、更に便利な機能を追加できる。
* Alexa Skill Kit を使って開発する。


.. _invocationname:

呼び出し名
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Alexa Sill を呼び出すための名前。「Alexa、<呼び出し名> を開いて」
* 呼び出し名の要件


    1. スキルの呼び出し名は、組織や個人の知的財産権を侵害するものであってはなりません。知的所有権ポリシーの詳細については、Alexa スキルのポリシーのテストを参照してください。
    
    2. 以下の場合を除いて、1語の呼び出し名は許可されません。

    * 呼び出し名がブランド／知的財産独自のもので、法的に根拠のある書類により、所有権が証明されているもの。
    * （ドイツ語のスキルのみ）呼び出し名が2語以上の結合形である。この場合、Alexaがその単語を認識するには、スキルの言語で実際に使われている単語である必要があります。

    3. 呼び出し名に、人名や場所の名前（「花子」、「東京」など）を単独で使用することはできません。ただし、それらを呼び出し名の一部として使用することは可能です（「神戸観光スポット」「花子流手作りショップ」など）。
    
    4. 原則として、呼び出し名に助詞を含めてはいけません。ただし、「の」は例外として含めることができます（「モリーのスペシャル星占い」「太郎の駅検索」など）。
    
    5. 呼び出し名に、Alexaスキルの起動フレーズとつなぎ語を含めることはできません。
    
    * 起動フレーズには「起動して」、「使って」、「開いて」、「スタート」、「スタートして」、「始めて」、「開始」、「開始して」、「実行して」、「実行」などがあります。
    * つなぎ語の例には「で」、「を使って」などがあります。スキルの起動フレーズとつなぎ語について詳しくは、ユーザーによるカスタムスキルの呼び出しを参照してください。
    
    6. 呼び出し名には、ウェイクワードである「アレクサ」、「アマゾン」、「エコー」、または「スキル」や「アプリ」という言葉を含めることはできません。

    7. 呼び出し名に使用できるのは基本的に、ひらがな、カタカナ、漢字のみです。アルファベットのアクロニムを使用する場合は、連続した小文字（「nasa」など）を使用します。イニシャリズムに関しては、以下の規則8と規則9をご覧ください。数字などの他の文字はスペルアウトする必要があります（「二十五」「パーセント」など）。呼び出し名はユーザーが正確に発話しやすく、他の言葉と聞き間違えられにくいものである必要があります。

    8. 呼び出し名に略語を含む場合は、アルファベット1文字の前後に半角スペースを挿入する必要があります（例：「今日の a b c クイズ」）。ただし、アルファベットが呼び出し名の最後に来る場合、最後のアルファベットの後ろに半角スペースは不要です（例：「あなたの傍の a t m」）。

    9. 既存のAlexa機能が起動してしまう可能性のある呼び出し名は使用しないでください。スキルの呼び出し名がよく使用されるAlexaのコマンドと重なると、ユーザーがAlexaからの応答に混乱したり、スキルを有効にできない場合があります。たとえば、スキルの呼び出し名が標準機能の「天気」コマンドと類似していると、Alexaから提供される情報が時にカスタムスキルから、時に標準の天気機能からと、異なる可能性があります。その結果、ユーザーは一貫した応答を得られず、ユーザーエクスペリエンスの低下につながります。

    10. 呼び出し名は、対象ユーザーの言語に合わせて作成してください。多言語対応のスキルの場合、呼び出し名に英語や他の言語が含まれていることがあります（固有名詞、製品名など）が、日本語で一般的に使用され、ユーザーにわかりやすい表記を選択してください（例：呼び出し名に「radio」という単語が含まれている場合、「ラジオ」と表記）。

    11. 呼び出し名は、ユーザーがスキルを必ず起動できるように、そのスキルに特有のものでなければなりません。一般的すぎる呼び出し名は、スキルの認定プロセスで却下されたり、スキルを見つけにくくなったりする原因になります。

* 参考：`カスタムスキルの呼び出し名を決定する <https://developer.amazon.com/ja-JP/docs/alexa/custom-skills/choose-the-invocation-name-for-a-custom-skill.html>`_


.. _interactionmodel:

対話モデル
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 参考：`カスタムスキルの呼び出し名を決定する <https://developer.amazon.com/ja-JP/docs/alexa/custom-skills/choose-the-invocation-name-for-a-custom-skill.html>`_


.. _intent:

インテント
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* ユーザーの音声によるリクエストを満たすアクションのこと。 スキルが処理するリクエスト。
* Amazon が予め用意した「標準ビルトイン・インテント」と開発者が独自に定義できる「カスタム・インテント」がある。

    * 標準ビルトイン・インテントの例：(ヘルプ) AMAZON.HelpIntent、(ストップ) AMAZON.StopIntent
    * カスタム・インテントの例：「コーヒーを注文して」・「コーヒー」：OrderIntent、「おすすめは何？」：RecommendIntent

* 参考：`スキルの対話モデルの作成 <https://developer.amazon.com/ja-JP/docs/alexa/custom-skills/create-the-interaction-model-for-your-skill.html>`_


.. _utterance:

サンプル発話
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* インテントに対応付けられた、会話に使用されると考えられる一連のフレーズ。

    * サンプル発話の例：「コーヒーを注文して」、「コーヒー」、「おすすめは何？」など


リクエスト
---------------------------------------------------

主に、3つのリクエストが飛んでくる。

* LaunchRequest：スキルの呼び出し名だけで発話がなかった場合
* IntentRequest：スキルの呼び出し名の後の発話にマッチするインテントがあった場合
* SessionEndedRequest：ユーザーによる終了。ユーザーの応答がない場合。コード内でエラーが発生した場合

LaunchRequest の例

.. code-block:: none

    "request": {
        "type": "LaunchRequest",
        "requestId": "amzn1.echo-api.request.fa901a5e-e907-4f1a-89b5-bf63a982562b",
        "timestamp": "2021-01-16T08:23:23Z",
        "locale": "ja-JP",
        "shouldLinkResultBeReturned": false
    }


IntentRequest の例

.. code-block:: none

    "request": {
        "type": "IntentRequest",
        "requestId": "amzn1.echo-api.request.cda73f02-1e25-480c-bf0a-dcb4e1b0a7a9",
        "locale": "ja-JP",
        "timestamp": "2021-01-16T08:24:31Z",
        "intent": {
            "name": "OrderIntent",
            "confirmationStatus": "NONE"
        }
    }


リクエスト処理
---------------------------------------------------

ハンドラー入力
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

リクエストハンドラー、リクエストと応答のインターセプター、エラーハンドラーにはすべて、呼び出し時に `HandlerInput` オブジェクトが渡される。

* RequestEnvelope：スキルの送信されるリクエスト本文全体を含む。

リクエスト本文は、例えば下記。

.. code-block:: none

    {
        "version": "1.0",
        "session": {
            // 省略
        },
        "context": {
            // 省略
        },
        "request": {
            "type": "IntentRequest",
            "requestId": "amzn1.echo-api.request.cd48e2c1-a98f-4230-b84d-83b4e7552ae7",
            "locale": "ja-JP",
            "timestamp": "2021-01-16T12:45:47Z",
            "intent": {
                "name": "OrderIntent",
                "confirmationStatus": "NONE"
            }
        }
    }


Alexa Skills Kit (ASK) SDK
---------------------------------------------------

下記が提供されている。

* ASK SDK for Node.js

    * `GitHub <https://github.com/alexa/alexa-skills-kit-sdk-for-nodejs>`_
    * `Docs <https://ask-sdk-for-nodejs.readthedocs.io/ja/latest/index.html>`_


* ASK SDK for Python
* ASK SDK for Java


ASK SDK for Node.js
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

index.js の基本的な書式。


.. code-block:: javascript

    // ①　SDK ライブラリを読み込む
    const Alexa = require('ask-sdk-core');  // core 機能のみを提供するライブラリ

    // ③ リクエストを処理するリクエストハンドラーを定義する
    const XXXX_Handler = {
        canHandle(handlerInput) {
            return handlerInput.requestEnvelope.request.type === 'XXXX_Request'
            && handlerInput.requestEnvelope.request.intent.name === 'XXXX_Intent';
        },
        handle(handlerInput) {

            // ここにアプリケーションのビジネスロジックを記述する
            // 例：注文する、情報を取得する、デバイスに命令を送る、メールする、など

            // Alexa に応答を返す。
            return handlerInput.responseBuilder
                .speak("Alexa に喋らせたいこと")
                .reprompt("ユーザにサイド尋ねるフレーズ")
                .getResponse();
        }
    };

    // ここに必要な数だけリクエストハンドラーを追加する。

    // ②スキルビルダーオブジェクトを生成する
    const skillBuilder = Alexa.SkillBuilders.custom();
    exports.handler = skillBuilder
        .addRequestHandlers(
            XXXX_Handler,
            // リクエストハンドラーを追加したら、ここにも名前を追加する
            // 上から順番に canHandle() をチェックして、true を返したハンドラーの handle() が実行される
      )
      .lambda();

* 参考：`Alexa道場 S02 EP09 ASK SDK for Node js を使おう <https://www.youtube.com/watch?v=itsNdkr8btU&list=PLan9XPu5-9zx-p7tJrjPAlMFvyELIck6t&index=10>`_





