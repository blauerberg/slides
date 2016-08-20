## Topics

* Drupal Consoleとは
* インストール方法
* ローカル上にサイトを立ち上げて基本的な使いかたを覚える

---

# About Drupal Console

![logo](https://drupalconsole.com/sites/default/files/inline-images/drupal-console.png) <!-- .element: style="background: none; border: none;" -->

---

## About Drupal Console

> The new CLI for Drupal. The Drupal Console is a tool to generate boilerplate code, interact and debug Drupal 8.
https://github.com/hechoendrupal/DrupalConsole <!-- .element: class="grow" -->

---

## Drupal Consoleとは

DrushのようにCLIでDrupalを操作するためのツールです。

Drupalのサイトをインストールしたり、モジュールのインストールやアップデート、キャッシュのクリアなど、サイトを構築、運用するために必要な一連の機能を提供します。

---

## Drushとの違い

ボイラープレートコードの生成機能を持っている点がDrushとの大きな違いです。

要するにrailsコマンドとかplay (activator)コマンドみたいなscaffoldingしてくれるやつです。

コマンド名はそのまま「drupal」です。

---

## なぜDrupal Consoleを使うのか

https://hechoendrupal.gitbooks.io/drupal-console/content/ja/about/why-should-you-care-about.html

---

## なぜDrupal Consoleを使うのか (超意訳)

「D8ってSymphony入ってすごい開発しやすくなったけど、その分覚える事多いしコード量も増えたじゃん。面倒だしミスりやすいから自動生成よね？」

---

### github上で公開、開発されています

![github](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/introduction_to_drupalconsole/drupalconsole_github.png) <!-- .element: style="width: 80%;" -->

https://github.com/hechoendrupal/DrupalConsole

---

### ものすごく活発に開発が進められています。

``` bash
$ git log --since=2015-07-01 --until=2016-06-30 --oneline --no-merges | wc -l
```

### ここ1年間のコミット数

* Drupal Core: 2118
* Drupal Console: 2526
* Drush: 622

なにげにcoreよりコミット多かったり..

---

### メンテナーの Eduardo Garcia (enzo) さんが来日してセッションを開催してくれました

![enzo](https://avatars1.githubusercontent.com/u/907914)

---

![drupal cafe vol.43](https://pbs.twimg.com/media/CdgEeCfVAAA78pJ.jpg:large) <!-- .element: style="width: 60%;" -->
https://groups.drupal.org/node/509188
http://enzolutions.com/talks/enzotour16-tokyo

---

![drupal cafe vol.43](https://pbs.twimg.com/media/Cd6Sg2eVIAArpwh.jpg) <!-- .element: style="width: 80%;" -->
https://groups.drupal.org/node/509190
http://enzolutions.com/talks/enzotour16-osaka

---

### 日本語のドキュメントを公開しました！

![document](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/introduction_to_drupalconsole/drupalconsole_document_ja.png) <!-- .element: style="width: 58%;" -->

https://drupalconsole.com/docs/japanese

---

## railsで新しいアプリを作成してサーバーを起動する

```bash
$ rails new myapp
$ cd myapp
$ bundle install
$ rake db:migrate
$ rails server
$ open http://localhost:3000

# titleとcontentフィールドを持つblogの Model, View, Controllerを生成
$ rails generate scaffold blog title:string content:text 
```

---

## Drupal Consoleで新しいサイトを作成して立ち上げる

```bash
$ drupal site:new mysite --latest
$ cd mysite
$ drupal site:install
$ drupal server
$ open http://localhost:8088

# blogモジュールを作成し、Model, View, Controllerを生成
$ drupal generate:entity:content --module blog
```

RailsのノリでDrupalの開発ができます。

(みなさんコードなんて自動生成したいですよね？)

---

# Drupal Consoleのインストール方法

---

## Mac & Linux

```bash
$ composer global require drupal/console:1.0.0-beta5
$ drupal init --override
```

https://hechoendrupal.gitbooks.io/drupal-console/content/ja/getting/project.html

---

## 注意点 (2016/8/20 時点)

- @stable がちょっと古いので1.0.0-beta5に読み替えてください。
- 「インストーラーを使ってローカルマシン上でプロジェクトを取得する方法を推奨します」となっていますが、1.0.0以降はComposerでのインストールが推奨です (というか、Composerで入れないと動きません)。

---

## Windows

```bash
$ composer global require drupal/console:1.0.0-beta5
$ drupal init --override
```

[公式ドキュメント](https://hechoendrupal.gitbooks.io/drupal-console/content/ja/getting/windows.html) の内容がちょっと古い(マージが間に合わず..)ので、[Pull Request中のドキュメント](https://github.com/blauerberg/drupal-console-book/blob/3f00a3686a3edd8d0d002cfbe2322545a5de2d1d/ja/getting/windows.md) を見てください。

---

## 注意点 (2016/8/20 時点)

- @stable がちょっと古いので1.0.0-beta5に読み替えてください。
- Windowsサポートのためのパッチを1.0.0-beta5のリリース後に入れましたが、次のリリースがまだなのでパッチを当てる必要があります。この手順は1.0.0-rcがリリースされれば不要になります。

```bash
$ cd ~/AppData/Roaming/Composer/vendor/drupal/console/
$ curl https://patch-diff.githubusercontent.com/raw/hechoendrupal/DrupalConsole/pull/2507.patch | patch -p1
$ curl https://patch-diff.githubusercontent.com/raw/hechoendrupal/DrupalConsole/pull/2567.patch | patch -p1
```

---

## 注意点その2 (2016/8/20 時点)

- GNU tar 1.29以上の場合にtarの展開に失敗するバグがあります。Git for Windowsの最新版だとこれに当たるので、[2.9.0](https://github.com/git-for-windows/git/releases/tag/v2.9.0.windows.1) をお使いください。
- SETXする時のコマンドプロンプトは管理者として実行してください(Windowsキー + x + Aで起動できます)
- php.iniでphp_curl.dllとphp_openssl.dllも有効にしてください (ごめんなさい、ドキュメント直します。。)

---

## なぜかWindowsサポートを頑張るハメに..

- [support Git Bash on windows](https://github.com/hechoendrupal/DrupalConsole/pull/2507)
- [prevent timeout of server process on windows](https://github.com/hechoendrupal/DrupalConsole/pull/2567)
- [update setup instructions for Windows](https://github.com/hechoendrupal/drupal-console-book/pull/218)
- [Translate ja/getting/windows.md](https://github.com/hechoendrupal/drupal-console-book/pull/219)

(メインのメンテナーがみんなWindows機を持っていないのは秘密)


---

## 日本語表示に切り替える

```bash
$ drupal settings:set language ja
$ drupal

Drupal Console (1.0.0-beta4) | 現在のDrupalのバージョン (8.1.3)
===============================================================

設定ファイルをユーザーのホームディレクトリにコピーする

   drupal init --override

Drupalをダウンロード、インストールしてサーバーを起動する

   drupal chain --file=~/.console/chain/quick-start.yml

Drupalプロジェクトを新規作成する

   drupal site:new drupal8.dev --latest
...
```

---

# 使ってみましょう

---

## 今回やってみること

* ローカルマシン上にDrupalのサイトをインストールする
* モジュールをインストールしてみる
* テーマをインストールして差し替えてみる
* ダミーのコンテンツを生成する
* Bootstrapのサブテーマを作成する
* オリジナルのモジュールを作成する

---

## 新しいサイトを作ってサーバーを起動するまで

* $ drupal chain --file=~/.console/chain/quick-start.yml
* コマンドを1回叩くだけ、1,2分でサイトが立ち上がります。

[Drupal 8をダウンロード、インストールして起動する](https://hechoendrupal.gitbooks.io/drupal-console/content/ja/using/how-to-download-install-and-serve-drupal8.html)

---

```bash
$ drupal chain --file=~/.console/chain/quick-start.yml

 // site:new
 drupal 8.1.7 をダウンロードしています

 [OK] Drupal 8.1.7 は /private/tmp/drupal8.dev にダウンロードされました

 // site:install
 Drupal 8のインストールプロセスを開始しています。

 [OK] Drupal 8のインストールが正常に完了しました。

 // server

 [OK] Executing php from /Users/aoyama/.phpenv/versions/7.0.2/bin/php.

PHP 7.0.2 Development Server started at Sat Jul 30 00:22:43 2016
Listening on http://127.0.0.1:8088
Document root is /private/tmp/drupal8.dev
```

---

## drupal chain の中身を覗いてみる

```bash
commands:
# Download Drupal
  - command: site:new
    arguments:
      directory: 'drupal8.dev'
    options:
      latest: 'true'
# Install Drupal
  - command: site:install
    options:
      langcode: en
      db-type: sqlite
      db-file: sites/default/files/.ht.sqlite
      site-name: 'Drupal 8 Quick Start'
      site-mail: admin@example.com
      account-name: admin
      account-mail: admin@example.com
      account-pass: admin
    arguments:
      profile: standard
# Start php built-in server
  - command: server
```

---

## chainは複数のコマンドの組み合わせ

~/.console/chain にサンプルが入ってます。
うまく活用すればセキュリティ更新など色々な作業が自動化が可能。

https://github.com/hechoendrupal/DrupalConsole/blob/master/config/dist/chain/quick-start.yml <!-- .element: style="word-wrap: break-word;" -->

---

## Drupal Consoleでできること

```bash
$ drupal list
```

https://hechoendrupal.gitbooks.io/drupal-console/content/ja/commands/available-commands.html <!-- .element: style="word-wrap: break-word;" -->

---

### モジュールをインストールしてみる

```bash
$ drupal module:install devel
$ drupal module:install admin_toolbar --latest
$ drupal module:install admin_toolbar_tools
```

---

### テーマをインストールして差し替えてみる

```bash
$ drupal theme:download bootstrap
$ drupal theme:install bootstrap  --set-default
```

---

### ダミーのユーザーやタクソノミー、コンテンツを生成する

```bash
$ drupal create:users
$ drupal create:terms
$ drupal create:nodes
```

---

### Bootstrapのサブテーマを作成する

```bash
$ drupal generate:theme --theme=my_bootstrap_subtheme --base-theme=bootstrap
$ drupal theme:install my_bootstrap_subtheme --set-default
```

---

### カスタムモジュールのスケルトンを作成する

```bash
$ drupal generate:module --module=mymodule
```

---

### カスタムモジュールにentity(モデル)のCRUD機能を追加

```bash
$ drupal generate:entity:content --module=mymodule --has-bundles --is-translatable

生成または更新されたファイル
 サイトのパス: /Users/aoyama/drupal8.dev
 1 - modules/custom/mymodule/mymodule.permissions.yml
 2 - modules/custom/mymodule/mymodule.links.menu.yml
 3 - modules/custom/mymodule/mymodule.links.task.yml
 4 - modules/custom/mymodule/mymodule.links.action.yml
 5 - modules/custom/mymodule/src/DefaultEntityAccessControlHandler.php
 6 - modules/custom/mymodule/src/DefaultEntityTranslationHandler.php
 7 - modules/custom/mymodule/src/Entity/DefaultEntityInterface.php
 8 - modules/custom/mymodule/src/Entity/DefaultEntity.php
 9 -
 modules/custom/mymodule/src/DefaultEntityHtmlRouteProvider.php
 10 - modules/custom/mymodule/src/Entity/DefaultEntityViewsData.php
 11 - modules/custom/mymodule/src/DefaultEntityListBuilder.php
 12 - modules/custom/mymodule/src/Form/DefaultEntitySettingsForm.php
 13 - modules/custom/mymodule/src/Form/DefaultEntityForm.php
 14 - modules/custom/mymodule/src/Form/DefaultEntityDeleteForm.php
 15 - modules/custom/mymodule/default_entity.page.inc
 16 - modules/custom/mymodule/templates/default_entity.html.twig
 17 - modules/custom/mymodule/templates/default-entity-content-add-list.html.twig
 18 - modules/custom/mymodule/mymodule.module
 19 - modules/custom/mymodule/mymodule.module
```

---

### どれくらいのコードが生成されたか見てみる

```bash
$ find modules/custom/mymodule -type f| xargs wc -l | tail -n 1
    1161 total
```

1000行のモジュールをバグやtypoなしに1分で書けますか？

---


## D8からは開発スタイルを変えましょう

* D7のようにモジュールをスクラッチから書くのは結構大変
* 複雑な機能を実現しようとすると、なんだかんだでSymphonyの知識は必須
* でもSymphonyのおかげでインターフェースが統一され、コードの自動生成はしやすくなった
* 他の開発フレームワークに習って、自動生成できるものはしていきましょう

---

### よく使うかもしれないコマンド集

```bash
$ drupal cache:rebuild # キャッシュクリア

$ drupal module:update {module} # コアやモジュールをアップデート

$ drupal module:install {module} # モジュールをインストール

$ drupal module:uninstall {module} # モジュールをアンインストール

$ drupal theme:download {theme} # テーマをダウンロード

$ drupal theme:install {theme} # テーマをインストール

$ drupal database:connect # DBに接続

$ drupal database:dump # DBをバックアップ

$ drupal database:restore # バックアップからDBをリストア

$ drupal cron:extcute # cront taskを実行

$ drupal generate:***** # いろいろ自動生成
```

---

# まとめ

---

## Drupal Consoleとは

* 今、一番簡単に早くD8を試すことができるツール
* インストールはすごく簡単
* MacやWindows上で動くローカルサイトを3分で立ち上げ可能
* PHPのビルドインサーバーとsqliteだけでも動くので、apacheとかmysqlは不要。サーバー苦手な人でも使える。
* railsのようなCLIでdrupalの開発ができる、自動生成最高！
* Drushにしかできないこと、Drupal Consoleにしかできないことがあるので併用がおすすめ
* 日本語のドキュメントを公開したよ！
