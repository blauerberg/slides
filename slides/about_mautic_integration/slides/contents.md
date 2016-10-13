## Topics

- オープンソースMAソフトウェア「Mautic」とは
- MauticとAPI
- APIを利用したMauticと他システムとの連携

---

## オープンソースMAソフトウェア「Mautic」とは

![mautic_logo](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_logo.png)

---

## 「Mautic」とは

- 2015年に公開された世界初のオープンソースのMAソフトウェア
- 日本語を含む52の言語に対応し、80,000以上の企業で導入されている

---

### 商用MAツールと遜色ない機能を提供

https://www.mautic.org/tour/

![mautic_our](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_tour.png) <!-- .element: style="width: 70%;" -->

---

- コンタクト管理 (個人情報、スコア付け、Webアクセス解析)
- Emailマーケティング
- ランディングページ作成
- フォーム管理
- アセット管理
- キャンペーン管理
- レポート作成
- ダイナミックコンテンツ
- etc..

---

## 商用MAツールとの違い

- オープンソースのためカスタマイズが可能
- オンプレミス環境での運用が可能
- Setup & Training Feeが無料
- コンタクトのデータ量に応じた従業課金がない

---

## Mautic Cloud Free/Pro

開発元が提供するクラウドサービス、サインアップから5分程度で利用可能

https://mautic.com/products/

---

## Mauticの動作環境
- PHP 5.6.19 or higher
- Mysql 5.5.3 or higher
- Apache 2.x or Nginx 1.x or Microsoft IIS 7.x

一般的なLAMP(LEMP)環境で動作
https://www.mautic.org/download/requirements/

---

## ソースコードはGPL v3ライセンスでgithubで公開

https://github.com/mautic/mautic
- 60 contributors
- 日本人のcontributorは3名くらい

---

## 過去のリリース実績

https://github.com/mautic/mautic/releases
- 2.1.0: 2016-08-12
- 2.0.0: 2016-07-05
- 1.4.0: 2016-05-11
- 1.3.0: 2016-03-02
- 1.2.0: 2015-09-03
- 1.1.0: 2015-06-07
- 1.0.0: 2015-03-10

---

## 日本語のリソース

- 日本語のドキュメント
  - https://mautic.org/docs/jp/index.html
- 日本語の翻訳 (翻訳率はほぼ100%、50名程度の翻訳者が活動中)
  - https://www.transifex.com/mautic/mautic/languages/
  - https://www.transifex.com/mautic/teams/33223/ja/
- 日本のコミュニティ
  - 東京、名古屋、札幌でMeetupイベントを開催中
  - https://www.facebook.com/groups/mautic.community.japan/
- 日本語フォーラム
  - https://jp.mautic.org/community
- Slackの日本語チャンネル (#jp)
  - https://www.mautic.org/slack/

---

## Mauticの登場によって変わった事

---

## 「誰でも」「すぐに」マーケティングオートメーションを始められるようになった。

---

# MauticとAPI

---

## Mauticが提供する他システムとの連携の方法

- REST API
- Webhook
- plugins

---

## REST API

- 認証はOauth 2が利用可能で、ユーザーの権限に対応したアクセス制御が可能
- PHP用にラップされた [APIライブラリ](https://github.com/mautic/api-library) が提供されている

---

https://developer.mautic.org/#rest-api

- Authorization (Oauth 2)
- Contacts
- Assets
- Campaigns
- Forms
- Segments
- Pages
- Point Actions
- Point Triggers

---

## Webhook

Mautic内で特定のイベントが発生したタイミングで、その情報をJSONのデータとして設定したURLにPostする。

通知できる情報は限られるが、受け側のシステムやサービスに受信するための処理を追加するだけで連携が可能。

https://developer.mautic.org/#webhooks

---

## plugins

Mautic内で特定のイベントが発生したタイミングでその情報を他のシステムに通知する。
pluginに対応しているサービスであれば設定だけで連携が可能なため、開発が不要。

https://developer.mautic.org/#plugins

---

標準で21個のpluginが添付。オンプレミス環境であれば、サードパーティから提供されるpluginや独自に開発したpluginの追加が可能。

![mautic_plugins](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_plugins.png) <!-- .element: style="width: 50%;" -->

---

MAツールをスタンドアーロンで運用するのではなく、既存のITインフラと接続し付加価値を生み出すことが重要。

![mautic_integration](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_integraiton.png) <!-- .element: style="width: 85%;" -->

---

## APIを利用した他システムとのインテグレーション例

---

## 1. SFA/CRMとの連携

Salesforce上の顧客データとMauticのコンタクト情報を同期。

既存のSFA/CRMの顧客情報とMauticのコンタクト情報を統合することが可能。

---

標準で提供されるSalesForce pluginを利用し、Force.com REST API経由でMauticとSalesForce間でコンタクト情報を双方向で同期する。


https://mautic.org/docs/en/plugins/salesforce.html

![mautic_sfdc](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_sfdc.png) <!-- .element: style="width: 100%;" -->

---

# Demo

---

## 2. ChatOps

Mauticに追加されたコンタクトの情報をリアルタイムにSlackに通知

---

Webhookでコンタクトの情報を送信し、SlackとのインターフェースはAPI Gateway + Lambdaでサーバーレスで構築。

![mautic_slack](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_slack.png) <!-- .element: style="width: 100%;" -->

---

# Demo

---

## 3. CMS連携

Drupalで構築したサイトに登録されたユーザーアカウント情報やフォームからの問い合わせ内容をMauticへ登録。

既存のWebサイトや会員制サイトのユーザー情報とMauticの情報を統合することが可能。

---

CMS (Drupal)内部のデータをREST API経由でMauticに送信。CMS側とMautic側のデータのマッピングをUIから変更できるため、データ構造の変更に柔軟に対応可能。

![mautic_drupal](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_api/mautic_drupal.png) <!-- .element: style="width: 100%;" -->

---

# Demo

---

## まとめ

- Mauticの登場により 「誰でも」「すぐに」マーケティングオートメーションを始められるようになった
- Rest API、Webhook、pluginなど複数の仕組みが提供されており、簡単に他のシステムと連携可能
- スタンドアローンで運用せず、APIを利用して既存のITインフラと接続し付加価値を生み出すがことが重要
