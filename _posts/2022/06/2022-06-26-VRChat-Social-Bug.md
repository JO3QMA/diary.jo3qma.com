---
layout: post
category: "VRChat"
tags: ["VRChat", "VRChat IPv6 Social Bug"]
title: "VRChat ソーシャルバグ対応策(多分)"
---

# VRChat ソーシャルバグ
VRChatのソーシャルが機能しない不具合があるらしいです。

自前環境では再現しないので、どうせ通信環境が悪いんだろうとか思っていましたが、PCに詳しいフレンドにも再現したので、どうやら本当らしいです。

## 現象

IPv6有効な環境でVRChat上で

- Social欄が更新されない
- Request Invite、Inviteが送られても来ない

以下のような感じらしいです。(相手に発生してる)
```text
相手 --Invite--> 自分 : ○
相手 --ReqInv--> 自分 --ReqInvOK--> 相手 : ☓
自分 --Invite--> 自分 : ☓ 
自分 --ReqInv--> 自分 : ☓ 
```

## 想定される原因
自分はネットワークに明るくない上に、自分の環境では再現しないので正しいかどうかはわかりませんが、Twitterやフレンドの話を聞いている限り、多分これです。

+ VRChat関係のIPv6の名前解決もしくはルーティングがおかしい。

IPv6の通信を無効化することで改善するという話があるので、ほぼ確でこれだと考えられます。

ただ、VRChatの1ユーザーである私が、VRChat本体の名前解決やルーティングの原因を解明し、根本的に解決することはできないため、

別の方法を考えます。

## 既知の対処法
すでに周知されている対処法をいくつか上げ、ダメな理由を書こうと思いましたが、お気持ちNoteになってしまいそうでしたので、割愛します。

IPv6への移行の妨げとなるような設定は行わないようにお願いします。

## 調査

### VRChatが通信している相手を見てみる

VRChat関係のIPv6の名前解決やルーティングでコケていると仮定した場合、IPv4で通信を行うようにしてあげればよいです。

それを行うには、VRChatがどこに向けて通信を行っているかを調べなければなりません。

VRChatがどこと通信しているかは`netstat`コマンドと`find`コマンド、アプリケーションのプロセスID(PID)を用いることで調べることができます。

PIDはタスクマネージャーの詳細タブで調べることができます。(起動毎にPIDは変わります。)

![taskmgr](/assets/img/2022/06/26/Taskmgr_QNhycbhMFo.png)

```console
netstat -nao | find "PID"
```

というコマンドをコマンドプロンプトで実行するとVRChatが現在通信している相手先、ポート、プロトコルを見ることができます。

```console
C:\Users\JO3QMA>netstat -nao | find "14776"
  TCP         127.0.0.1:56426        127.0.0.1:56427        ESTABLISHED     14776
  TCP         127.0.0.1:56427        127.0.0.1:56426        ESTABLISHED     14776
  TCP         127.0.0.1:56434        127.0.0.1:56435        ESTABLISHED     14776
  TCP         127.0.0.1:56435        127.0.0.1:56434        ESTABLISHED     14776
  TCP         192.168.1.25:57050     35.241.52.229:443      ESTABLISHED     14776
  TCP         [2400:4150:5363:3b00:8592:13c0:d8cf:3622]:56453  [2606:4700::6812:1a24]:443  ESTABLISHED     14776
  TCP         [2400:4150:5363:3b00:8592:13c0:d8cf:3622]:56455  [2606:4700::6812:1a24]:443  ESTABLISHED     14776
  UDP         0.0.0.0:63324          *:*                                    14776

C:\Users\JO3QMA>
```

実行結果を見ると、いくつか自分(`127.0.0.1`)に向けて通信している謎の通信はありますが、

`35.241.52.229`と`2606:4700::6812:1a24`の443ポート(HTTPS)に向けて通信しているようです。

このIPをどの団体に割当られているかはwhoisで調べることができます。が、Windows 10には`whois`コマンドが実装されていないため、

`Windows Subsystem for Linux` (WSL)を用いて調べることにします。WSLの有効化方法は割愛します。

```bash
jo3qma@Theta:~$ whois 35.241.52.229 | grep "OrgName"
OrgName:        Google LLC
jo3qma@Theta:~$ whois 2606:4700::6812:1a24 | grep "OrgName"
OrgName:        Cloudflare, Inc.
jo3qma@Theta:~$
```

ということで、`35.241.52.229`はGoogle、`2606:4700::6812:1a24`はCloudflareに割り当てられているIPということがわかりました。

今回、IPv6による通信が問題と思われるため、後者のCloudflare関係がおかしいと推察できます。

### パケットキャプチャして見てみる

Wiresharkというパケットキャプチャを用いて、名前解決を覗いてみます。

オンラインゲームは当たり前ですが、外のサーバーと通信しています。もちろんこういったサービスでは固定IPを割り振ってもらって運用するものですが、ゲームにIPアドレスを直書きすることはまあ無いと思います。

もし、何らかの理由でIPアドレスを変更しなければならない場合など、アップデートを挟まなければなりませんし、IPアドレスを直書きしている場合、IPv6対応などがめんどくさそうなので。

なので、一般には公開しないドメインを使ってサーバーとの通信を行っている可能性が高いです。今回はそれを目論んでパケットキャプチャでDNSプロトコルのリクエストなどを覗いてみます。

私も今回初めてWiresharkを使用してパケットキャプチャを行ったので、もっと良い方法があるかもしれません。

パケットキャプチャを開始する前に、以下のコマンドを実行しておきます。

```console
ipconfig /flushdns
```

このコマンドは、DNSキャッシュを削除するものです。

通常は以下の順番で名前解決を試みるのですが、2番目のPC内のDNSキャッシュがある場合は3番目以降に見に行かないため、
欲しいパケットがキャプチャできません。なので、削除しておきます。

1. hosts
1. PC内のDNSキャッシュ
1. ルーター内のDNSキャッシュ
1. ISPのDNSサーバー

Wiresharkの使用法などはめんどくさいのと、まさかりが飛んでくるのが怖いので割愛します。

1. DNSキャッシュのクリア
1. Wiresharkでパケットキャプチャ取得開始
1. VRChat起動
1. VRChatで任意の時間遊ぶ
1. Wiresharkでパケットキャプチャの取得を終わる

の順番で良いと思います。

後は適当にdnsプロトコルでフィルターを掛け、CSVで出力します。

では、DNSレコードを覗いてみましょう。

例によって、WindowsにはawkがないのでWSLを使用します。PowerShellを使えば高度なことができるんでしょうが、そこまでしてPowerShellを使いたくないので。

```bash
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$ grep "response" test.csv | grep "vrchat" | awk -F'\",\"' '{print$7}' | awk '{print$6,$7,$8}' | sort | uniq -c | sort -r
     60 api.vrchat.cloud A 104.18.26.36
     57 api.vrchat.cloud A 104.18.27.36
     50 api.vrchat.cloud AAAA 2606:4700::6812:1a24
     46 api.vrchat.cloud AAAA 2606:4700::6812:1b24
      7 assets.vrchat.com CNAME d14hwloucscbyy.cloudfront.net
      2 pipeline.vrchat.cloud AAAA 2606:4700::6812:1b24
      1 pipeline.vrchat.cloud A 104.18.27.36
      1 pipeline.vrchat.cloud A 104.18.26.36
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$
```

適当にVRChat関連は\*.vrchat.\*だろうと目星をつけてそれ以外を弾いています。

見たところ、以下のドメインに対して名前解決を試みたようです。

- api.vrchat.cloud
- pipeline.vrchat.cloud
- assets.vrchat.com

`assets.vrchat.com`はCNAMEでCloudFrontに別名がついているようです。
今回のものとは関係なさそうなので、パスします。

残った2ドメイン

- api.vrchat.cloud
- pipeline.vrchat.cloud

に関してはAレコード及びAAAAレコードで名前解決が行われているようです。

次に、`dig`コマンドを用い、DNSの正引きを行ってどのようなIPアドレスで名前解決が返されるか見てみます。

AレコードとAAAAレコードを見ます。grepは必要ない行を弾いてるだけなので気にしないでください。
```bash
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$ dig api.vrchat.cloud a | grep -v "^;" | grep -v '^\s*$' 
api.vrchat.cloud.       0       IN      A       104.18.26.36
api.vrchat.cloud.       0       IN      A       104.18.27.36
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$ dig api.vrchat.cloud aaaa | grep -v "^;" | grep -v '^\s*$' 
api.vrchat.cloud.       0       IN      AAAA    2606:4700::6812:1a24
api.vrchat.cloud.       0       IN      AAAA    2606:4700::6812:1b24
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$ dig pipeline.vrchat.cloud a | grep -v "^;" | grep -v '^\s*$' 
pipeline.vrchat.cloud.  0       IN      A       104.18.26.36
pipeline.vrchat.cloud.  0       IN      A       104.18.27.36
jo3qma@Theta:/mnt/c/Users/JO3QMA/Desktop$ dig pipeline.vrchat.cloud aaaa | grep -v "^;" | grep -v '^\s*$' 
pipeline.vrchat.cloud.  0       IN      AAAA    2606:4700::6812:1b24
pipeline.vrchat.cloud.  0       IN      AAAA    2606:4700::6812:1a24
```

ということで、

- api.vrchat.cloud
- pipeline.vrchat.cloud

に関してはAレコードもAAAAレコード共に同じIPに向いていることがわかりました。

複数のIPアドレスが列挙されている点については、DNSラウンドロビンというロード・バランシングの機能のひとつなので、どちらにアクセスして帰って来る結果は同じです。

### 結論

最初の仮定が正しいならば、

- api.vrchat.cloud
- pipeline.vrchat.cloud

これら2ドメインに対するIPv6通信をできないようにすれば良さそうです。

個人的に追記するならば、`files.vrchat.cloud`に対する通信も一度だけ確認し、AレコードとAAAAレコードが上記2ドメインと同じだったため、これも追加した3ドメインをどうにかしてやれば良さそうです。

## 対処法
### ルーターを使用してAAAAレコードの問い合わせをrejectする
多分これが一番スマートな解決法です。

DNSのAレコードが書き換わっても何もせずとも対処できます。

問題点はそんな高度な設定をするためにはYAMAHAのRTXシリーズなど業務用ルーターが必要なことですね。

HMDと同じぐらいの値段で売ってるので買いましょう。そこら辺の民生品とは比べ物にならない安定度を誇ります。

### DNSサーバーを建てる
`*.vrchat.cloud`のAAAAレコードを消したDNSサーバーを建ててやればよいです。

自宅サーバーをお持ちの逸般の家庭の諸氏には余裕でしょう。

問題点といえば、自宅サーバーを運用できるような人は業務用ルーターも持っていることが多いため、この手段より上記手段のほうが良いでしょう。

### hostsを書き換える
hostsファイルを書き換える事により、DNSを見に行く前にIPを決め打ちしてやります。

管理者権限が必要となりますが、確実に今までの手段より楽です。

管理者権限をお持ちでない人はおかあさんにお願いしましょう。

`C:\Windows\System32\drivers\etc\hosts`に以下を追記します。

```text
104.18.27.36  pipeline.vrchat.cloud
104.18.27.36  api.vrchat.cloud
104.18.27.36  files.vrchat.cloud
```

実行結果:
```console
C:\Users\JO3QMA>netstat -ano | find "28884"
  TCP         127.0.0.1:52297        127.0.0.1:52298        ESTABLISHED     28884
  TCP         127.0.0.1:52298        127.0.0.1:52297        ESTABLISHED     28884
  TCP         127.0.0.1:52316        127.0.0.1:52317        ESTABLISHED     28884
  TCP         127.0.0.1:52317        127.0.0.1:52316        ESTABLISHED     28884
  TCP         192.168.1.25:51306     104.18.27.36:443       ESTABLISHED     28884
  TCP         192.168.1.25:51307     104.18.27.36:443       ESTABLISHED     28884
  TCP         192.168.1.25:51311     13.33.211.105:443      ESTABLISHED     28884
  TCP         192.168.1.25:52311     13.33.211.177:443      ESTABLISHED     28884
  TCP         192.168.1.25:52324     104.18.27.36:443       ESTABLISHED     28884
  TCP         192.168.1.25:52325     104.18.27.36:443       ESTABLISHED     28884
  TCP         [2400:4150:5363:3b00:e1f4:27f8:96da:2a57]:51322  [2404:6800:4004:826::200e]:443  ESTABLISHED     28884
  TCP         [2400:4150:5363:3b00:e1f4:27f8:96da:2a57]:51323  [2404:6800:400a:16::a]:443  ESTABLISHED     28884
  TCP         [2400:4150:5363:3b00:e1f4:27f8:96da:2a57]:51324  [2404:6800:4004:820::2003]:80  ESTABLISHED     28884
  UDP         0.0.0.0:9000           *:*                                    28884
  UDP         0.0.0.0:51116          *:*                                    28884
  UDP         0.0.0.0:60698          *:*                                    28884
```

うまいこと機能していると思いますし、VRChatは一応正常に遊べています。

`2404:6800:400x::`とIPv6で通信していますが、whois見る限りGoogleなので、問題ないでしょう。

なお、ここまで書いておいてアレですが、hostsに記載するIPアドレスには気をつけましょう。セキュリティ的なリスクを孕みます。

できる限り、自分で調べたIPアドレスを記載するようにしてください。

