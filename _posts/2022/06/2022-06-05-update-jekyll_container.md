---
layout: post
tag: "jekyll"
title: "Jekyll用コンテナを改良した"
---

# 汎用的に使えるようにした
今日、珍しく暇だったのでJekyllのコンテナをいじっていました。

永遠と「これ、LPICで見たやつだ！」みたいな感じで書いてました。LPICって案外実務的ですね。

[今まで](https://github.com/JO3QMA/docker-jekyll/tree/b370303585110f6b217b9bb62b9f65948730a179)は、`/usr/local/app`にDockerからサイトのデータをマウントし、ビルドを行っていました。

今回は、Gitを使用してクローンしてくるようにしました。

また、Chirpy以外のテーマも利用できるように、テーマ側もGitでクローンできるように改良しました。

まだいじり足りないので、[chirpyブランチ](https://github.com/JO3QMA/docker-jekyll/tree/chirpy)においていますが、満足したらmasterブランチにマージします。

なお、例のごとくREADMEは書いてません。気が向いたら書きます。どうせ、自分しか使わないので困りませんので。

次は、サイトデータ置いてるGitの更新を見て自動でPullしてビルドするようにしたいですね。