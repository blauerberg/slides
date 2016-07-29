## Topics

* Drupal Consoleとは
* インストール方法
* 日本語化について
* Drupal Consoleでローカル上にサイトを立ち上げる
* Drupal Consoleの機能紹介

---

# About Drupal Console
Drupal Consoleとは

![logo](https://drupalconsole.com/sites/default/files/inline-images/drupal-console.png) <!-- .element: style="background: none; border: none;" -->

---

## Drupal Consoleとは

> The new CLI for Drupal. The Drupal Console is a tool to generate boilerplate code, interact and debug Drupal 8.
https://github.com/hechoendrupal/DrupalConsole <!-- .element: class="grow" -->

---

DrushのようにCLIでDrupalを操作するためのツールです。

ボイラープレートコードの生成機能を持っている点がDrushとの大きな違いです。

要するにrailsコマンドとかplay (activator)コマンドみたいなやつです。

コマンド名はそのまま「drupal」です。

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

* Drupal core: 2118
* Drupal Console: 2526
* Drush: 622

なにげにcoreよりコミット多かったり..

---

### メンテナーの Eduardo Garcia (enzo) さんが来日してセッションを開催してくれました

![drupal cafe vol.43](https://pbs.twimg.com/media/CdgEeCfVAAA78pJ.jpg:large) <!-- .element: style="width: 60%;" -->
https://groups.drupal.org/node/509188
http://enzolutions.com/talks/enzotour16-tokyo

---

![drupal cafe vol.43](https://pbs.twimg.com/media/Cd6Sg2eVIAArpwh.jpg) <!-- .element: style="width: 80%;" -->
https://groups.drupal.org/node/509190
http://enzolutions.com/talks/enzotour16-osaka

---

### 日本語のドキュメントが近日公開予定！

![document](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/introduction_to_drupalconsole/drupalconsole_document.png) <!-- .element: style="width: 58%;" -->

https://drupalconsole.com/docs

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

$ drupal generate:entity:content --module blog # blogモジュールを作成し、Model, View, Controllerを生成
```

RailsのノリでDrupalの開発ができます。

(みなさんコードなんて自動生成したいですよね？)

---

# Drupal Consoleのインストール方法

---

## Drupal Consoleをインストール

```bash
$ curl https://drupalconsole.com/installer -L -o drupal.phar
$ php -r "readfile('https://drupalconsole.com/installer');" > drupal.phar
$ mv drupal.phar /usr/local/bin/drupal
$ chmod +x /usr/local/bin/drupal
$ drupal init --override

or 

$ composer global require drupal/console:@stable
$ drupal init --override

or

$ git clone https://github.com/hechoendrupal/DrupalConsole.git
$ cd DrupalConsole
$ composer install
$ drupal init --override
```

https://hechoendrupal.gitbooks.io/drupal-console/content/en/getting/project.html

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

## 新しいサイトを作ってサーバーを起動するまで

Download, install and serve Drupal 8:

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

コマンドを1回叩くだけ、2,3分でサイトが立ち上がります。

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

https://github.com/hechoendrupal/DrupalConsole/blob/master/config/dist/chain/quick-start.yml <!-- .element: style="word-wrap: break-word;" -->

---

## chainは複数のコマンドの組み合わせ

~/.console/chain にサンプルが入ってます。
うまく活用すればセキュリティ更新など色々な作業が自動化が可能。

---

## Drupal Consoleでできること

```bash
$ drupal list
```

https://hechoendrupal.gitbooks.io/drupal-console/content/en/commands/available-commands.html <!-- .element: style="word-wrap: break-word;" -->

---

### よく使うコマンド集

```bash
$ drupal cache:rebuild # キャッシュクリア
$ drupal module:update {module} # コアやモジュールをアップデート
$ drupal module:download {module} # モジュールをゲット
$ drupal module:install {module} # モジュールをインストール
$ drupal module:uninstall {module} # モジュールをアンインストール
$ drupal theme:download {module} # テーマをダウンロード
$ drupal theme:install {module} # テーマをインストール
$ drupal theme:uninstall {module} # テーマをアンインストール
$ drupal database:connect # mysqlでDBに接続
$ drupal database:dump # DBをバックアップ
$ drupal database:restore # バックアップからDBをリストア
$ drupal cron:extcute # cront taskを実行
$ drupal generate:***** # CLIから色々作れます
```

---

# まとめ

---

## Drupal Consoleとは

* 今、一番簡単に早くD8を試すことができるツール
* インストールはすごく簡単
* MacやWindows上で動くローカルサイトを3分で立ち上げ可能
* PHPのビルドインサーバーとsqliteだけでも動くので、apacheとかmysqlは不要。サーバー苦手な人でも使える。
* railsのようなCLIでdrupalの開発ができる
* Drushにしかできないこと、Drupal Consoleにしかできないことがあるので併用がおすすめ
* 日本語のドキュメントを近日公開予定！
