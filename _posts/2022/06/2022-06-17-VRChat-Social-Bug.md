---
layout: post
category: "VRChat"
tags: ["VRChat", "VRChat IPv6 Social Bug"]
title: "IPv6有効時にVRChatのソーシャルが更新されない件"
---

# ソーシャルがバグる
この件、自分の環境で起きていないんですけど、周りの人の環境で起きているのでメモ書き。

IPv6接続を有効にしている時に、Socialタブが更新されない・Invite/ReqInviteの通知が飛んでこないというもの。

軽く検証した感じ、自分の環境では起きていないため、それ以上の検証・実験が行いようが無いのですが、書いておきます。

## 事象

```
相手 --Invite--> 自分 : ○
相手 --ReqInv--> 自分 --ReqInvOK--> 相手 : ☓
自分 --Invite--> 自分 : ☓ 
自分 --ReqInv--> 自分 : ☓ 
```

書いてるママですね。相手に通知行くのが失敗している感じです。

## ダメだったやつ
api.vrchat.cloudの名前解決を自前で書き換える。

api.vrchat.cloudはVRChatのAPI関連のエンドポイントです。(たぶん)

DNSを正引きした感じ、IPv6にも割り振られていたので、それをhostsで書き換えてやって、IPv4に向くようにしてみます。

```bash
jo3qma@Theta:~$ nslookup api.vrchat.cloud
Server:         172.29.224.1
Address:        172.29.224.1#53

Non-authoritative answer:
Name:   api.vrchat.cloud
Address: 104.18.27.36
Name:   api.vrchat.cloud
Address: 104.18.26.36
Name:   api.vrchat.cloud
Address: 2606:4700::6812:1a24
Name:   api.vrchat.cloud
Address: 2606:4700::6812:1b24
```

```C:\Windows\System32\drivers\etc\hosts
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#    127.0.0.1       localhost
#    ::1             localhost

104.18.27.36     api.vrchat.cloud
```

自前環境で見た感じ、一部通信はIPv6ではなくIPv4を通るようになりましたが、IPv6を使う通信が残っているっぽかったです。

api.vrchat.cloud以外にそのIPv6アドレスに紐付けられたドメインがあるんでしょうかねぇ。

