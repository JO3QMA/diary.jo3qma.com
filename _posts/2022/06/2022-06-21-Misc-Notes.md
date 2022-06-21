---
layout: post
category: "日記"
tag: "日記"
title: "2022/06/21 雑記"
---

# 2022/06/21 雑記
今日は雨です。

折り畳み傘が壊れてから、いままでめんどくさいという理由で、新しいものを購入していませんでしたが、そろそろ購入しないと辛くなってきそうです。

## Cloudflare障害

本日15時頃に世界的に有名なCDNサービスをしている[Cloudflareが障害](https://k-tai.watch.impress.co.jp/docs/news/1418919.html)を起こしました。

それに伴い、DiscordやVRChat、pixivなどのサービスが落ちたらしいです。その時間は仕事していたので知りませんが。

クソの役にも立たないDowndetectorも落ちてたらしいです。なんでやねん。

16:20頃にはCloudflareは復旧したようです。

### 余談

このブログ([diary.jo3qma.com](https://diary.jo3qma.com))もCloudflareのCDNを前段に噛ませてます。

無料枠なのでお気持ち程度ですが、オリジンサーバーを隠すという意味もあります。

一方、[jo3qma.com](https://jo3qma.com)にはCDNを通していません。

よって、jo3qma.comを見ることができるのに、diary.jo3qma.comを見ることができない場合、Cloudflareの障害という可能性が高まります。

なお、[Cloudflare System Status](https://www.cloudflarestatus.com/)で詳細なCloudflareの障害状況を見ることができます。

### あるサイトがCloudflareを使っているかどうかを調べる。
`traceroute`の一番最後のIPを`whois`して`OrgName`探してやるとCFになってます。多分。

```bash
jo3qma@Theta:~$ traceroute diary.jo3qma.com
traceroute to diary.jo3qma.com (172.67.195.62), 64 hops max
  1   172.22.128.1  0.104ms  0.075ms  0.096ms
  2   192.168.1.1  0.548ms  0.435ms  0.467ms
  3   *  *  *
  4   *  *  *
  5   *  *  *
  6   *  *  *
  7   *  *  *
  8   *  *  *
  9   172.67.195.62  7.685ms  7.528ms  5.920ms
jo3qma@Theta:~$ whois 172.67.195.62 | grep "OrgName"
OrgName:        Cloudflare, Inc.
jo3qma@Theta:~$
```

## WACCA
SEGAの音ゲーの一つ、WACCAが8/31でオンラインサービス終了だそうです。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">【重要なお知らせ】<br>この度、『WACCA』は、2022年8月31日（水）をもちまして、オンラインサービスを終了させていただくこととなりました。<br>オンラインサービス終了までのスケジュール及び、オフライン版に関する詳細についてはこちらをご確認ください。<a href="https://t.co/7PDYrsudet">https://t.co/7PDYrsudet</a><a href="https://twitter.com/hashtag/WACCA?src=hash&amp;ref_src=twsrc%5Etfw">#WACCA</a></p>&mdash; WACCA公式 (@WACCA_official) <a href="https://twitter.com/WACCA_official/status/1539186383935668225?ref_src=twsrc%5Etfw">June 21, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

あの、CHUNITHMとmaimaiを足して3で割った感じのやつです。

1クレだけやりましたが、忙しくて頭が追いつかなかった記憶があります。

何時行っても空いてたのでしゃーないかなって感じはします。