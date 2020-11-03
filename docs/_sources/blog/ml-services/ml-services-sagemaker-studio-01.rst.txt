[第9回] Amazon SageMaker Studio の基本的な使い方 (1)
==============================================================

はじめに
-------------------

第5〜8回目の連載では、Amazon SageMaker のチュートリアルを使って、基本的な使い方の紹介をさせていただきました。

    * `「【第5回】Amazon SageMakerの基本的な使い方を理解する(1)」 <https://news.mynavi.jp/itsearch/article/devsoft/5099>`_
    * `「【第6回】Amazon SageMakerの基本的な使い方を理解する(2)」 <https://news.mynavi.jp/itsearch/article/devsoft/5101>`_
    * `「【第7回】Amazon SageMakerの基本的な使い方を理解する(3)」 <https://news.mynavi.jp/itsearch/article/devsoft/5113>`_
    * `「【第8回】Amazon SageMakerの基本的な使い方を理解する(4)」 <https://news.mynavi.jp/itsearch/article/devsoft/5115>`_

今回からの数回に渡って、昨年の AWS re:Invent 2019 で発表された新機能である「Amazon SageMaker Studio」について扱いたいと思います。

.. これまでは、前回までに扱ったノートブックインスタンスを構築するスタイルだった。
.. ノートブックインスタンスを利用する開発では、利用者がノートブックインスタンスの構築と管理が必要であった。
.. 利用者は機械学習を利用したサービス (コード) の開発がしたい
.. ノートブックインスタンスの構築や管理は価値を生まない作業 (Undifferentiated Lifting) なのでしたくない
.. Amazon SageMaker Studio という IDE の登場により、利用者はそのような作業を AWS に任せて価値の創出に注力することができる。

.. Amazon SageMaker Studio の登場により開発スタイルが2種類になった。

.. * ノートブックインスタンスを利用する従来からの開発スタイル
.. * Amazon SageMaker Studio を利用する新しい開発スタイル

.. Amazon SageMaker Studio は、Amazon SageMaker が提供する様々な機能とシームレスに連携しており、利用者はより便利に使えるようになった。
.. 機械学習を利用するサービスの開発者の UX を変える機能であると言える。
.. まだ東京リージョンには対応していないが、機械学習は AWS における注力分野の1つであるため、近い将来に対応するものと考えられる。
.. 知っておくことは有用であると考えられる。

下記を理解していただくことを目標にして解説をしていきます。

    * Amazon SageMaker Studio の概要と基本的な利用方法がわかる。
    * Amazon SageMaker Autopilot などの関連機能の概要や基本的な利用機能もわかる。


Amazon SageMaker Studio とは
----------------------------------------

Amazon SageMaker Studio は、AWS re:Invent 2019 で発表された Amazon SageMaker の提供する統合開発環境 (IDE) です。

.. figure:: ../../../images/9th-sagemaker-studio-gui-example.jpg
  :width: 900px

  Amazon SageMaker Studio の GUI

同イベントでは、Amazon SageMaker Studio の他にもたくさんの機能が追加されて、Amazon SageMaker が大幅に機能強化されました。
下記に主な新機能の概要を示します。
いくつかの機能は次回以降の連載に登場しますので、ここでは概要の提示に留めます。

- Amazon SageMaker Notebooks : Amazon SageMaker Studio に統合されたフルマネージドなノートブック
- Amazon SageMaker Experiments : 学習ジョブの実行履歴や設定、結果を一元管理
- Amazon SageMaker Debugger : 学習の監視、記録、分析と異常の検知
- `Amazon SageMaker Autopilot <https://aws.amazon.com/jp/sagemaker/autopilot/>`_ : AutoML 機能による機械学習モデルの自動構築
- Amazon SageMaker Model Monitor : 本番環境にデプロイした推論エンドポイントの継続的な監視

Amazon SageMaker Studio は、これらの機能とシームレスに連携して機械学習モデルの開発・学習・推論に加え、維持・運用まで含めて効率的に行うことができます。

.. figure:: ../../../images/9th-aws-ml-stack-sagemaker-studio.jpg
  :width: 900px

  AWS の ML スタック (AWS 社主催のセミナー資料等を参考に作成)


Amazon SageMaker Studio の登場の背景
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

前述の通り、Amazon SageMaker Studio は IDE としての機能を提供します。
前回までに Amazon SageMaker の基本的な使い方を説明しましたが、開発環境に相当するものは「ノートブックインスタンス」でした。
すなわち、Amazon SageMaker を利用する場合の開発スタイルが2種類あるように見えます。

* ノートブックインスタンスを利用する従来からの開発スタイル
* Amazon SageMaker Studio を利用する新しい開発スタイル

| ここからは Amazon SageMaker Studio がもたらす変化について詳細を見ていきたいと思います。
| まずは、従来型のノートブックインスタンスを利用する開発スタイルにまつわる課題を見ていきましょう。(下図にオレンジ色の吹き出しで記載)

.. figure:: ../../../images/9th-sagemaker-usage-problem.jpg
  :width: 900px

  Amazon SageMaker を利用する際の課題とその所在

下表に課題の詳細を記載します。上記の吹き出しの番号と対応付けて確認してください。

.. list-table:: ノートブックインスタンスでの開発にまつわる課題の詳細
    :header-rows: 1
    :widths: 10 30 60

    * - 項番　　　　　
      - 主な関連工程
      - 問題点
    * - 1
      - | 開発
        | 維持・運用
      - * 利用者がノートブックインスタンスのサイジングを行い、作成する必要がある。
        * ノートブックインスタンスのリソースの利用状況のモニタリングを継続的に行い、必要に応じてスケールアップなどの対処を行う必要がある。
    * - 2
      - 全体
      - * 開発は主にノートブックインスタンス上で行うが、過去に実行した学習ジョブの結果や現在デプロイされている推論エンドポイントの情報を確認したい場合はコマンドを実行するか、AWS マネジメントコンソールでのページ遷移が必要となる。
        * 学習を繰り返す度に機械学習モデルが増えるので、ハイパーパラメータや精度などの情報の一元管理をしたくなるが、AWS マネジメントコンソールではそれができない。Excel や Jupyter など情報を管理する仕組みを用意する必要がある。
    * - 3
      - | 開発
        | 維持・運用
      - * 開発者の増員とともに IAM ユーザの数が増えるため、作成や管理が必要となる。機械学習を専門とするエンジニアなど Amazon SageMaker 以外のサービスの操作が不要な場合でも IAM ユーザの払い出しが必要となる。
        * 当然、AWS マネジメントコンソールへのログインと Amazon SageMaker への画面遷移が必要である。


Amazon SageMaker Studio の導入メリット
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

| ここに記載した作業 (課題) は価値を生まない作業であるばかりか、開発者にとってはストレスとなるため、可能な限りなくしたい作業です。
| Amazon SageMaker Studio は、上記の課題に対するソリューションを提供してくれます。利用者は価値を生まない作業から解放され、価値の創出に注力することができます。


ノートブックインスタンスの作成・管理が不要
++++++++++++++++++++++++++++++++++++++++++++++++++

Amazon SageMaker Studio では、「Amazon SageMaker Notebooks」という JypyterLab ベースのフルマネージドなノートブックが提供されます。
ノートブックインスタンスと同様の特徴、Amazon SageMaker Notebooks に固有の特徴について下記に示します。

**ノートブックインスタンスと同様の特徴**

* JupyterLab ベースのノートブック環境が提供されるため、同じ要領で開発をすることができる。既存のノートブックファイルを持ち込みも可能である。
* AWS SDK for Python (Boto 3) や Amazon SageMaker Python SDK などの開発に必要な SDK やツールがプリインストールされている。
* カーネル、すなわち、Conda による仮想的な実行環境が提供される。

.. figure:: ../../../images/9th-sagemaker-studio-notebooks-launcher.jpg
  :width: 900px

  Amazon SageMaker Notebooks の Launcher 画面

.. figure:: ../../../images/9th-sagemaker-studio-notebooks-kernel.jpg
  :width: 300px

  Amazon SageMaker Notebooks で選択可能なカーネル

**Amazon SageMaker Notebooks に固有の特徴**

* 利用者が明示的にノートブックインスタンスを作成する必要がない。
* 「高速起動タイプ (Fast Launch)」と呼ばれるインスタンスタイプが用意されており、通常は2分以内に高速起動するよう設計されている。
* 永続的な記憶領域として Amazon EFS が利用されており、ノートブックをシャットダウンしても他のユーザやサービスからファイルにアクセス可能である。
* ワークロード (対象) に合わせてインスタンスタイプ (汎用/コンピューティング最適化/高速コンピューティング) を選択することができる。変更も数クリックで可能である。(下図を参照)
* 「ローカルモード」 (後述) での実行がサポートされていない。

.. figure:: ../../../images/9th-sagemaker-studio-notebooks-instance-type.jpg
  :width: 900px

  Amazon SageMaker Notebooks のインスタンスタイプの選択


開発・学習・推論の大部分の作業が Amazon SageMaker Studio で完結
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

下図に示すように、機械学習ワークフローの大部分のタスクをカバーしており、利用者は Amazon SageMaker Studio だけで作業を完結することができます。

.. figure:: ../../../images/9th-sagemaker-studio-service-coverage.jpg
  :width: 900px

  機械学習ワークフローにおける Amazon SageMaker Studio のカバー範囲


従来型の開発スタイルで機械学習モデルの開発を進める場合は、下記のいずれかの方法で対応します。

* ノートブックから Python のコードか AWS CLI で操作する。(前回紹介した方法)
* AWS マネジメントコンソール (Amazon SageMaker のコンソール) で画面操作をする。 

機械学習モデルは一度学習して終わることはなく、精度の改善のために試行錯誤を何度も繰り返します。
その結果、下図のように学習ジョブ (トレーニングジョブ) を多数作成することになります。

.. figure:: ../../../images/9th-sagemaker-training-jobs.jpg
  :width: 900px

  Amazon SageMaker のコンソール画面 (トレーニングジョブ)

このようにジョブが増えてくると、ハイパーパラメータなどの条件やその際の精度を把握しきれなくなるため、情報を整理して一覧化したくなります。
Amazon SageMaker では検索機能は提供されていますが、情報を一元管理する機能は提供されていませんでした。
情報の参照にはそれぞれのページへの遷移が必要であり、Excel や Jupyter で管理する仕組みを実装する必要がありました。

.. figure:: ../../../images/9th-sagemaker-search.jpg
  :width: 900px

  Amazon SageMaker の検索機能

下図に示すように、Amazon SageMaker Studio では情報を一元管理する機能が提供され、画面遷移をせずにここだけで情報が完結します。
情報は自動で追加・更新されて一覧化されるため、利用者はそれを参照するだけです。
ドリルダウンをして詳細な情報を参照したり、グラフを作成することもできます。

.. figure:: ../../../images/9th-sagemaker-studio-jobs-list.jpg
  :width: 900px

  Amazon SageMaker Studio の学習ジョブの一覧


.. figure:: ../../../images/9th-sagemaker-studio-aws-settings.jpg
  :width: 900px

  Amazon SageMaker Studio の学習ジョブの詳細 (AWS Settings)


IAM ユーザを持たない利用者でも利用が可能
+++++++++++++++++++++++++++++++++++++++++++++++++++

Amazon SageMaker Studio では、`AWS Single Sign-On (SSO) <https://aws.amazon.com/jp/single-sign-on/>`_ をサポートしており、外部の ID プロバイダと連携してユーザの認証を行うことができます。
もちろん通常の AWS サービスと同様に IAM ユーザでの利用も可能です。

.. figure:: ../../../images/9th-sagemaker-studio-sso.jpg
  :width: 900px

  Amazon SageMaker Studio のセットアップ画面

AWS SSO は SAML 2.0 をサポートしており、Active Derectory などの既存のディレクトリサービスや GitHub や Slack などのアプリケーションと連携することも可能です。
また、Amazon SageMaker Studio に直接アクセスするための URL が払い出されます。

例えば、機械学習は専門的な知識が必要であるため、外部からエンジニアを一時的に雇用する場合があるかと思います。
AWS SSO を利用すると、下記のようなメリットがあります。

* 管理者目線：IAM ユーザを作成する必要がないため、管理する対象を増やさずに済む。
* 開発者目線：AWS マネジメントコンソールへのログインと Amazon SageMaker Studio への画面遷移という一手間を省くことができる。

なお、AWS SSO は2020年9月に東京リージョンに対応しましたので、Amazon SageMaker Studio が東京リージョンに対応後にはこの方法も選択肢に含めることができます。 (参考：「`AWS Single Sign-On が東京リージョンで利用できるようになりました <https://aws.amazon.com/jp/blogs/news/aws-single-sign-on-tokyo/>`_」)


ノートブックインスタンスと Amazon SageMaker Studio の使い分け
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ここまで Amazon SageMaker Studio や Amazon SageMaker Notebooks の導入メリットについて見てきました。
機械学習モデルの開発を便利に行うことができるため、これから開発する方やご紹介したメリットに魅力を感じる方は、Amazon SageMaker Studio を利用して開発することが良いと考えられます。
(本稿の執筆時点で東京リージョンに対応していないため、東京リージョンでの対応が前提です)

ただし、細かな違いや制約があるため、それらを踏まえてもビジネス要件が満たせ、メリットを享受できることを確認した上で利用してください。

例えば、料金です。
Amazon SageMaker Studio も背後でインスタンスが稼働しており、そのインスタンスに対する課金は発生します。
インスタンスに対する費用は同一水準で提供されていますが、ストレージに違いがあります。

ノートブックインスタンスは ML ストレージ (EBS ボリュームに相当)、Amazon SageMaker Studio は Amazon EFS を利用します。
Amazon EFS の方が割高であるため、同一構成とした場合は Amazon SageMaker Studio を利用した方がコスト高となります。
割高である分、ここまでに紹介したメリットを享受できるため、差額はそれに対する費用と考えると良いと思います。

参考までに、バージニア北部リージョンでの価格を記載します。
料金は見直されることがあるため、最新情報は「`Amazon SageMaker の料金 <https://aws.amazon.com/jp/sagemaker/pricing/>`_」で確認してください。

.. list-table:: インスタンスの料金の例 (バージニア北部リージョン)
    :header-rows: 1
    :widths: 30 30

    * - インスタンスタイプ
      - 料金
    * - ml.t3.medium [USD/時間]
      - 0.0582
    * - ml.g4dn.xlarge [USD/時間]
      - 0.7364
    * - ml.m5.large [USD/時間]
      - 0.1344
    * - ml.c5.large [USD/時間]
      - 0.119

.. list-table:: ML ストレージの料金  (ノートブックインスタンスで利用)
    :header-rows: 1
    :widths: 30 30

    * - ML ストレージ
      - 料金
    * - 汎用 (SSD) ストレージ [USD/GB・月]
      - 0.14

.. list-table:: Amazon EFS の料金 (Amazon SageMaker Studio で利用)
    :header-rows: 1
    :widths: 30 30

    * - Amazon EFS のストレージクラス
      - 料金
    * - 標準ストレージ [USD/GB・月]
      - 0.30
    * - 低頻度アクセスストレージ [USD/GB・月]
      - 0.025
    * - 低頻度アクセスリクエスト [転送 GB あたり]
      - 0.01
    * - プロビジョニングスループット [MB/秒・月]
      - 6.00


また、Amazon SageMaker Studio は「ローカルモード」をサポートしていません。
ローカルモードとは、学習や推論をノートブックインスタンスのローカルでテストする機能です。

Amazon SageMaker での学習や推論は、それぞれ専用のインスタンスを作成して、そのインスタンス上で処理を行います。
すなわち、ノートブックインスタンスの費用とは別に学習と推論の費用もかかります。

開発したコードの妥当性が確認できていない状況で大規模にテストをすると費用がかさんでしまう可能性があります。
ローカルモードを利用すると、ノートブックインスタンス上で学習や推論を行うことができるため、これを回避することができます。
今後のアップデートで機能追加がされる可能性もありますが、現状はサポートしていないので注意が必要です。


まとめ
-----------------

今回はAmazon SageMaker Studio や Amazon SageMaker Notebooks の概要を中心に解説をしました。
次回からは実際に Amazon SageMaker Studio を使っていきたいと思います。


+++++++++++

.. include:: ../author/author.rst
