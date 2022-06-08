---
layout: post
category: "自作PC"
tag: "BitLocker"
title: "BitLockerから締め出された"
---

# BitLockerから締め出された
## 事の発端
久しぶりにM/Bのページ見てたらBIOSの更新が来ていたので、「BIOSアップデートするか〜ｗ」ということでBIOSアップデートをしました。

BIOSアップデート自体は、BIOSファイルをUSBメモリーに突っ込んで、BIOS画面からアップデートを選択するだけなので簡単に終わりました。

これ自体はASRockのマザボを6年ぐらい使っているのでなれたものです。

そして、起動しようとすると・・・。

デンッ！！
![BitLocker Recovery Key Display](/assets/img/2022/06/07/PXL_20220607_041550766.jpg)

BitLockerの回復キーを求められました。

BitLocker、使ってること普通に忘れてたし、回復キーはPCの中。

```
　　終
制作・著作
━━━━━━━━━
　ⓃⒽⓀ
```

うーん・・・。終わったな。

## BitLockerの回復キーはPCの中。
アホなので、BitLockerの回復キーをPCの中にしか保管していません。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">BitLockerの回復キー、BitLockerの中かもしれん・・・</p>&mdash; BitLockerかけたままBIOSアップデートをしてはいけない (@x86q) <a href="https://twitter.com/x86q/status/1533987342448742401?ref_src=twsrc%5Etfw">June 7, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
世界一アホ。

メインPCには、

- システムドライブ
- VRChatのキャッシュ用ドライブ
- ゲーム用ドライブ

の3つのSSDが刺さっています。
少なくとも

- 2つのドライブでBitLocker暗号化をしていること
- 確実にシステムドライブはBitLockerで暗号化している
- BitLockerの回復キーは幸いにもシステムドライブ以外に保存していること

がわかっています。

さて、どこにある回復キー。

## 救済手段
MicrosoftアカウントにBitLockerの回復キーがあることが多いというネットの記事が・・・！

これは勝ったなワハハ。ということで見に行くことに。

![Microsoft Account BitLocker Page](/assets/img/2022/06/07/microsoft_account.png)

GG!対あり！

ありませんでしたとさ。おしまい。

## Ubuntuで覗く
BitLockerで暗号化されていない、残されたドライブに回復キーがあることを願いながら、USBメモリーにUbuntu 20.04 Liveイメージを突っ込んで、起動したら・・・。

ありました。

![BitLocker Revocery Key](/assets/img/2022/06/07/bitlocker_recovery-key.png)

やったぜ。

## 解決
Ubuntu 20.04 Liveを起動しているUSBメモリーに回復キーをコピって、ノートパソコンで表示してGot事なき。

ちなみに、VSCodeで開かなかったら文字化けしてた。VSCode曰く`UTF-16 LE`かつ`CRLF`らしいです。

まあ、回復キーは数字の羅列なので、他の文字が読めなくとも問題がないわけですけれども。

## 反省
BitLockerの回復キーは別デバイスに保存しよう。

多分一番いい解決方法は印刷して金庫の中に突っ込むのがいい。

というか、BitLockerの回復キーの入力画面で、非暗号化されたディスクのテキストファイルを開くぐらいの操作はさせてくれ。