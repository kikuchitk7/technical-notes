AWS Management Console へのログイン通知
==============================================

E-mail での通知
-------------------------

概要
^^^^^^^^^^^^^^^
- CloudWatch Events でのログインイベントの検知をトリガに E-mail 通知する。
- CloudWatch Events から SNS に連携する。
- SNS から E-mail に連携する。

手順
^^^^^^^^^^^^^^^
- SNS でトピックを作成する。
- SNS でサブスクリプション (Email) を作成する。
- CloudWatch Events のルールを作成する。
- サインアウト・再ログインする。
- E-mail が届いたことを確認する。

E-mail での通知 (情報の整形)
----------------------------------

概要
^^^^^^^^^^^^^^^^
- CloudWatch Events + SNS での通知はメッセージが JSON 形式なので可読性が低い。
- CloudWatch Events から Lambda に連携してメッセージの整形を行う。

手順
^^^^^^^^^^^^^
- IAM ロールを作成する。
- Lambda で関数を作成する。
- CloudWatch Events のルールを作成する (修正する)。
- サインアウト・再ログインする。
- E-mail が届いたことを確認する。

補足
^^^^^^^^^^^^^
- ログイン時に下記の JSON が連携される。

.. code-block:: json

    {
        "version": "0",
        "id": "6f87d04b-9f74-4f04-a780-7acf4b0a9b38",
        "detail-type": "AWS Console Sign In via CloudTrail",
        "source": "aws.signin",
        "account": "123456789012",
        "time": "2016-01-05T18:21:27Z",
        "region": "us-east-1",
        "resources": [],
        "detail": {
            "eventVersion": "1.02",
            "userIdentity": {
            "type": "Root",
            "principalId": "123456789012",
            "arn": "arn:aws:iam::123456789012:root",
            "accountId": "123456789012"
            },
            "eventTime": "2016-01-05T18:21:27Z",
            "eventSource": "signin.amazonaws.com",
            "eventName": "ConsoleLogin",
            "awsRegion": "us-east-1",
            "sourceIPAddress": "0.0.0.0",
            "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36",
            "requestParameters": null,
            "responseElements": {
            "ConsoleLogin": "Success"
            },
            "additionalEventData": {
            "LoginTo": "https://console.aws.amazon.com/console/home?state=hashArgs%23&isauthcode=true",
            "MobileVersion": "No",
            "MFAUsed": "No"
            },
            "eventID": "324731c0-64b3-4421-b552-dfc3c27df4f6",
            "eventType": "AwsConsoleSignIn"
        }
    }


参考
-------------
- 「`AWS CloudWatch Events + Amazon SNSで、AWS管理コンソールのサインインを検知しメールで通知する <https://dev.classmethod.jp/articles/check-amc-signin-using-cloudwatch-events/>`_」
- 「`Lambdaを使ってAmazon SNSからメールを飛ばしてみた <https://qiita.com/tsumita7/items/bbafba094db5794d0374>`_」
- 「`AWS】CloudWatchアラーム通知をLambdaでSlack投稿する <https://www.geekfeed.co.jp/geekblog/aws_cloudwatch_to_slack/>`_」
