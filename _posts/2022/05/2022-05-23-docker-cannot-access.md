---
layout: post
title: "自分向けDockerでアクセスできないトラブルメモ"
---

# サービスに外からアクセスできない
Dockerコンテナ内で動かしているサービスに外からアクセスできない時の原因切り分けメモです。

完全に自分向け

## 構成

![Network IMG](/assets/img/2022/05/23/chrome_rcdcDfjbxZ.png)

自宅サーバー関連のネットワークは上の図のようになっています。
RTX1200にグローバルIPが振られ、サーバー内ではnginx-proxyというコンテナが80番、443番のポートに対するアクセスのリバースプロキシを行っております。

RTX1200,物理サーバーにそれぞれファイアーウォールが存在し、Dockerにもポートの開閉の機能があります。

nginx-proxyコンテナはfront-nginxというDockerのネットワークに所属しており、そのネットワークに所属したコンテナを常に監視し、リバースプロキシをよしなにしてくれます。

同じネットワークに接続されたコンテナ同士は相互に通信をすることができ、内部ではコンテナ名もしくはホスト名で名前解決を行うことができます。

### 502 Bad Gateway
この状態のときは、少なくともnginx-proxyまでのアクセスができています。

まあ、nginxのエラー画面が出ているので当たり前ですね。

ということは、front-nginxネットワーク、もしくは対象のコンテナの問題と考えることができます。

#### 他のコンテナを見る
同じfront-nginxネットワークに所属している他のコンテナにアクセスできれば、front-nginxネットワークは問題ないことがわかります。

#### docker ps
```bash
docker ps
```
を実行することで、コンテナのポート開放状況を見ることができます。
対象のポートが開いていなければ、docker-compore.ymlで`ports`の指定し忘れか、Dockerfileでの`EXPOSE`し忘れと原因を突き止めることができます。

例:
```bash
$ docker ps
CONTAINER ID   IMAGE                                    COMMAND                  CREATED        STATUS                  PORTS                                                                       NAMES      
b73dba6dd3ca   jekyll_jekyll                            "/bin/bash /entrypoi…"   23 hours ago   Up 23 hours             4000/tcp                                                                    jekyll-jekyll-1        
4c05a3233220   jrcs/letsencrypt-nginx-proxy-companion   "/bin/bash /app/entr…"   32 hours ago   Up 26 hours                                                                                         letsencrypt
f8bc0b0b4202   jwilder/nginx-proxy                      "/app/docker-entrypo…"   32 hours ago   Up 26 hours             0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp    nginx-proxy
f95685496a36   redmine                                  "/docker-entrypoint.…"   4 days ago     Up 26 hours             3000/tcp                                                                    redmine-redmine-1      
4aad4d5abf14   postgres                                 "docker-entrypoint.s…"   4 days ago     Up 26 hours             5432/tcp                                                                    redmine-redmine_db-1           
```
#### netstat
対象コンテナ内でnetstatすることにより、そもそもサービスが目的のポートを使用しているかしていないかを調べることができます。
```bash
apt install net-tools -y
netstat -ant | grep "LISTEN"
```
このコマンドを実行することにより、受信に使用しているポートとプロセスがわかります。

例:
```bash
$ netstat -ant | grep "LISTEN"
tcp        0      0 0.0.0.0:4000            0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.11:36929        0.0.0.0:*               LISTEN
```

#### nmap
同じfront-nginxネットワークに所属しているコンテナから、nmapを使用してポートの状態などを調べることができます、
```bash
aot install nmap -y
nmap -p 4000 [Container ID]
```
例えば、対象ポートを開放しているのに対象ポートで受信しているサービスがいないなどわかります。
- filterd: ポートが閉じてる
- closed: ポートは開いているが受信できるサービスがいない
- open: ポートが開いている上に受信できるサービスがある

など

例:
```bash
$ nmap -p 80,4000 jo3qma.com
Starting Nmap 7.80 ( https://nmap.org ) at 2022-05-23 21:38 JST
Nmap scan report for jo3qma.com (58.88.222.2)
Host is up (0.012s latency).
Other addresses for jo3qma.com (not scanned): 2400:4150:5363:3b00:866:12ff:fe9b:81d2
rDNS record for 58.88.222.2: p155002-ipngn200501osakachuo.osaka.ocn.ne.jp

PORT     STATE    SERVICE
80/tcp   open     http
4000/tcp filtered remoteanything

Nmap done: 1 IP address (1 host up) scanned in 1.26 seconds
```

#### サービスがローカルループバックアドレスを向いている
考えたら当たり前なのですが、Dockerコンテナ内のサービスが`127.0.0.1`などのローカルループバックアドレスで開いていると、ホストや他のコンテナ、外部ネットワークから見ることができません。
当たり前ですね。

`0.0.0.0`を指定してやるなどするとみることができます。


---
終わり。

またなんか沼ったら追記するかも。