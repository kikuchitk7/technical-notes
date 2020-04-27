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

参考
-------------
- 「`AWS CloudWatch Events + Amazon SNSで、AWS管理コンソールのサインインを検知しメールで通知する <https://dev.classmethod.jp/articles/check-amc-signin-using-cloudwatch-events/>`_」
