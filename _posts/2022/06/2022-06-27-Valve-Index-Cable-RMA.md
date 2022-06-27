---
layout: post
category: "VRChat"
tags: ["Valve Index", "VR"]
title: "保証期間外Valve Index RMAメモ"
---

# 保証期間外Valve Index RMAメモ
タイトル通りですが、保証期間外のValve Index(のケーブル)が故障したため、RMAしてみます。

Valve Index一式を入手したのは2022/08/20で、保証期間は一年なので、もうすでに過ぎています。

ケーブルを購入、あわよくば無償交換してもらいたいなってお気持ちがあります。

## 参考URL
インターネットとは便利なもので、先人たちが残してくれた情報を自由に漁ることができます。

私も過去にValve Index関係で2回、BaseStation関係で1回、Valveに対しRMA申請を出していますが、流石に必要な情報などは忘れたものもあるので、参考にします。

- [ValveIndex動作不良のための返品交換の流れ | kura 3D's BLOG.](https://kura3d.site/2019/12/31/valve-index-return-processing/)
- [tips_RMA_ValveIndex - 求聞持Wiki](https://wiki.thakyuu.invr.chat/Ti2ps/tips_RMA_ValveIndex)
- [Valve Index HMDにノイズが入るようになったのでケーブル交換した件](https://sechiro.fanbox.cc/posts/2242376)

## 必要な情報

- 症状
- 故障したパーツのシリアル番号(ケーブルはIndex本体)
- 個人情報
  - 名前
  - 住所/郵便番号
  - 電話番号
  - メールアドレス
- 購入店舗
- 購入日時
- お買上げ明細書の写真
- SteamVRのレポート
- msinfo32の結果

## 事前に調べておくと楽なこと
これは症状によると思います。

今回はケーブルの断線が原因ということで。

- グラフィックドライバー(最新版)の再インストール
- ケーブルを抜き差ししてしっかりさして見る
- SteamVRをベータ版にし、`設定`->`ビデオ`->`DisplayPortトレーニングモード`の各設定を試す。

これらを調査しておき、最初のリクエストに入れておくことで、スムーズに進むと思います。多分。

## 流れ

1. Steamのサポートに投げる(Web/Steamクライアント)
1. Steamのサポートが納得したら日本代理店であるDegica Shopに投げられる
1. Degica Shopからメールが届く
1. メールの中身に従って、故障部品を梱包し送る
1. Degica Shopから代替部品が帰って来る

この流れです。

Degicaに送って3-4日で帰ってきました。

営業日の関係で休日を挟むと伸びるかもしれません。

## ログ
Steamサポートとのやり取りログです。
途中から英語で送っていますが、英語で対応してもらったほうが早いからという理由です。

### 6月17日 11時06分 JO3QMA -> Steam Support
```text
ENGLISH REPLY OK!!!

-- JAPANESE --

こんにちは。

2020/08/20日に購入したValve Indexにつきまして、
ヘッドマウントディスプレイにおいて、下記の問題が生じております。

1. 音声が途切れる
2. 赤色や緑色のノイズが発生する

以下のことを試しましたが、改善しませんでした。

1. グラフィックドライバの再インストール
1. Valve Indexのケーブルをしっかり刺し直す

また、SteamVRが使える他のPCを持っていないので試すことはできませんでした。

修理部品を購入、もしくは交換できないでしょうか？

よろしくお願いします。

購入店舗などの情報

氏名: アリス
住所: 日本国大阪府西大阪市XX-XX-XX
郵便番号: 012-3456
電話番号: 012-3456-7890
Valve Index's Serial Number: XXXX0123456X
購入店舗: Degica Shop
購入日時: 2020/08/20

-- ENGLISH --
hello.

Regarding the Valve Index purchased on 08/20/2020
The following problems have occurred in the head-mounted display.

1. Audio is interrupted
2. Red or green noise is generated

I tried the following, but it didn't improve.

1. Reinstall the graphics driver
1. Re-stick the Valve Index cable firmly

Also, I didn't have another PC that can use SteamVR, so I couldn't try it.

Can I buy or replace repair parts?

I would appreciate your favor.

Information such as the store of purchase

Name: Alice
Address: XX-XX-XX Nishi-Osaka City, Osaka Prefecture, Japan
Zip code: 012-3456
Phone number: 012-3456-7890
Valve Index's Serial Number: XXXX0123456X
Store of purchase: Degica Shop
Purchase date: 2020/08/20
```

\+各種添付ファイル

### 6月17日 14時40分 Steam Support -> JO3QMA
```text
現在、日本語サポートを提供できる担当者が不在のため、できるだけ早くお客様をサポートできるように英語で返答しています。日本語でのサポートが必要な場合は、お手数ですが改めてお知らせください。今後の回答が確実に翻訳されるようにいたします。

Thank you for reaching out and letting us know about this issue. we can definitely look into all possible options for you which would depend on your warranty coverage. But before that, we would like to provide the following troubleshooting steps first.

Please try the following before writing back to us:

Check all headset connections and ensure the plug behind your face gasket is firmly seated.
Update your video drivers to the latest available versions.
Reboot your headset by unplugging and replugging the breakaway connector.
Retest the issue.

Finally, if none of the above helps improve the issue, please do the following as a last step. Opt into the SteamVR beta by right-clicking SteamVR in your Library and selecting Properties > Betas, then selecting SteamVR Beta Update from the drop-down. Exit SteamVR and wait for the update to complete. You'll find a firmware update ready for your headset. After installing that firmware, enter VR and use the dashboard to navigate to Settings > Video > DisplayPort Training Mode and test the issue using each setting available there to check if there is any improvement.

Please let us know the results.

Running on coffee,
Bob
```

### 6月18日 10時55分 JO3QMA -> Steam Support
```text
hello.
Thank you for your reply.

I tried what I was told, but it didn't improve...
```

### 6月19日 11時25分 Steam Support -> JO3QMA
```text
Thank you for following the instructions above and reporting your testing result to us.

I'm sorry that you're still seeing issues.

Before further assistance, please confirm your email address.

Once we receive your reply, we'll help you accordingly. We appreciate for your cooperation.

Steam Support,
Charlie
```

### 6月19日 12時34分 JO3QMA -> Steam Support
```text
thank you for your reply!

My email address is "example@example.com".

thank you.
```
### 6月19日 18時44分 Steam Support -> JO3QMA

```text
情報を共有して頂いてありがとうございます。

お客様の返品保証（RMA）は、Degicaに引き継がれました（RMA ID - XXXXXX012-3456）。

Degicaのサポートチームからの連絡をお待ちください。保証期間が終了している場合は、Degicaの判断で交換をお断りする場合がございますのでご了承ください。

Valveハードウェアをご利用いただきありがとうございます。

Steam Support
Dave
```

## 届いた

届きました。

ケーブルのみ交換してくれました。去年は本体ごと交換でしたので、Valveからケーブルのみにするように指示があったのでしょうか。

![Cable](/assets/img/2022/06/27/PXL_20220625_151214258.jpg)

ケーブルの留具も送ってくれました。2つも！

壊れるので使いませんが。

![Cable](/assets/img/2022/06/27/PXL_20220625_151240509.jpg)


## おわり
サポートに投げてから8日ぐらいで交換部品が届きました。

実質VR不可能期間は3日程度だったのでまあ悪くないとおもいます。

個人的にはサポートに投げるのめんどくさいので、補修部品売ってくれないかな！！！！
