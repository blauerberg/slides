## Topics

Drupal 8で使えるBootstrapのサブテーマを作ってみます。

---

## ドキュメント

https://www.drupal.org/theme-guide/8 

---

## D7のテーマとのファイル構造的な違い

- {theme_name}.info -> {theme_name}.info.yml
- template.php -> {theme_name}.theme

---

## {theme_name}.info.yml

```
name: my_bootstrap_subtheme
type: theme
description: My Boostrap sub theme
package: Custom
base theme: bootstrap
core: 8.x
version: 8.x-1.0
regions:
  navigation: 'Navigation'
  navigation_collapsible: 'Navigation (Collapsible)'
  header: 'Top Bar'
  highlighted: 'Highlighted'
  help: 'Help'
  content: 'Content'
  sidebar_first: 'Primary'
  sidebar_second: 'Secondary'
  footer: 'Footer'
  page_top: 'Page top'
  page_bottom: 'Page bottom'
libraries:
  - my_bootstrap_subtheme/global-styling
```

---

## テーマにcssやjsなどのアセットを追加する場合

- libraries を {theme_name}.info.ymlにキーとして追加
- 値は他のテーマと重複しないように {theme_name}/{library_name} にする

```
libraries:
  - my_bootstrap_subtheme/global-styling
```

---

## {theme_name}.libraries.yml

```
global-styling:
  css:
    theme:
      css/custom_style.css: {}
  js:
    js/custom_script.js: {}
```

---

## css/custom_style.css

メニューのサイト名の部分をオレンジっぽく変える

```
.navbar-default .navbar-brand {
  color: #ff8c00;
}
```

---

## js/custom_script.js

コンテンツエリアの先頭にBootstrapのアラートメッセージを入れてみる

```
(function($){
  console.log("execute my custom script");
  $(".main-container").prepend($("<div>").addClass("alert alert-info").attr("role", "alert").text("execute my custom script"));
})(jQuery);
```

---

## 出来上がったものはこちら

https://github.com/blauerberg/my_bootstrap_subtheme
