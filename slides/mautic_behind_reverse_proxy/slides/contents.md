## Topics

MauticとトラッキングするWebサーバーをリバースプロキシーの下に置いたらわりと面倒だったので、
その辺りのネタをお話します。

---

# 超シンプルな構成

---

![mautic_simple_diagram](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_behind_reverse_proxy/mautic_simple_diagram.png) <!-- .element: style="width: 100%;" -->

---

## MauticとトラッキングするWebサーバーがプロキシー配下のプライベートIPなゾーンにある場合

---

<!-- .slide: data-background="https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_behind_reverse_proxy/mautic_complex_diagram.png" data-background-size="100%" -->

---

# どんな問題が起こったか

---

# 問題その1

---

## 同一IPに対してanonymousリードが1件しか登録されない

---

![problem1](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_behind_reverse_proxy/mautic_behind_rever_proxy_problem1.png) <!-- .element: style="width: 90%;" -->

これはリバースプロキシーの有無に関係なく発生する話ですが、Mauticは同じIPアドレスからのanonymousリードは同一のリードとして扱います。これはバグではなくdesign(仕様)です。

---

## どんな時に問題になるか

- 大きめの企業や学校など、単一の固定グローバルIPの下にたくさんのユーザーがいる場合、誰のアクセスか識別できない
- 固定ではないグローバルIPからのアクセスの場合、そのIPが違う人に付与されると全くの他人からのアクセスが同一のanonymousリードとして管理される可能性がある

---

## 技術的な細かい話

- [getCurrentLead()](https://github.com/mautic/mautic/blob/2.1.1/app/bundles/LeadBundle/Model/LeadModel.php#L617) の実装を見てみましょう。
- FingerPrintやJavascript Trackingの機能を使ってもこの問題は解決しません。

---

## どうやって解決したか

https://github.com/mautic/mautic/pull/1030 のパッチを適用。

最近 https://github.com/mautic/mautic/issues/2406 にissueも登録されたので、やはりみなさんお困りの様子。

---

# 問題その2

---

## リードのIPが全てリバースプロキシーのIPになる

---

![problem2](https://s3-ap-northeast-1.amazonaws.com/assets.blauerberg.github.io/mautic_behind_reverse_proxy/mautic_behind_rever_proxy_problem2.png) <!-- .element: style="width: 100%;" -->

---

## 技術的な細かい話

- [getIpAddressFromRequest](https://github.com/mautic/mautic/blob/2.1.1/app/bundles/CoreBundle/Helper/IpLookupHelper.php#L74) の実装を見てみましょう。
- HTTP_CLIENT_IP, HTTP_X_FORWARDED_FOR, HTTP_X_FORWARDED, HTTP_X_CLUSTER_CLIENT_IP, HTTP_FORWARDED_FOR, HTTP_FORWARDED, REMOTE_ADDR の順にチェックされる
- 各変数から近隣ノード (カンマ区切りの最後の部分)のIPアドレスを取得
- プライベートIPや予約エリアのIPの場合、その変数は無視され次の変数がチェックされる

---

## 今回の構成だと変数の値はこんな感じ

```
HTTP_X_FORWARDED_FOR: 1.2.3.4, 210.123.32.147, 192.168.100.1
REMOTE_ADDR: 210.123.32.147
```

- 1.2.3.4: ユーザーのIP
- 210.123.32.147: リバースプロキシーのグローバルIP
- 192.168.100.1: リバースプロキシーのプライベート側IP

※設定される値は機器やサーバーの設定によります。

---

## getIpAddressFromRequest のロジックを適用すると

- HTTP_X_FORWARDED_FOR -> 近隣ノードのIPアドレス(192.168.100.1)がプライベートなので無視される
- REMOTE_ADDR -> プロキシーのグローバルIP (210.123.32.147) がリードのIPとして認識される

---

## どうやって解決したか

nginxで$proxy_add_x_forwarded_forの先頭のIPアドレスをHTTP_CLIENT_IPに設定してphpに渡す。
```
map $proxy_add_x_forwarded_for $origin_addr {
  default "";
  ~^(?<first_addr>.*?),.* $first_addr;
}

server {
  ...
  fastcgi_param HTTP_CLIENT_IP $origin_addr;
  ...
}
```

---

## まとめ

- 2.1.1時点では同一IPは同一リードとして扱われる、困る場合は要パッチ。
- プロキシー配下にある場合、環境変数を調整しないとリードのIPが期待通り取れない場合があるので注意。必要に応じてWebサーバー側で設定を行いましょう。
