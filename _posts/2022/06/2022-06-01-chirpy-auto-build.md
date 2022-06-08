---
layout: post
category: "ブログ"
tags: "Jekyll", "Docker"
title: "Chirpy用のJekyllをいい感じにしてくれるコンテナを作った"
---

# Chirpyを使いたいが、gemを追加するだけでは使えない
[Chirpyというテーマを使うことにしました](/posts/2022/05/29/changed-jekyll-theme/)が、Chirpyを使うにはリポジトリをForkし、それを元に記事のファイルを追加する方法がメインです。

いやまあ、それでもいいんだけど、本家Chirpyのアップデートのときに地獄のmergeをしたくないので、別の方法をとりました。

## 脳筋的解決法
Jekyll的には別にリポジトリにChirpyが収まっている必要はなく、ビルド時にChirpyのファイルが存在すれば良いみたいです。

なので、
- Chirpyの最新タグをcloneする
- ビルド用ディレクトリにコピーする
- サンプルの記事を消す
- ビルド用ディレクトリに記事や設定ファイルなどを上書きコピーする
- `bundle install`する
- `jekyll build`する

ということをしてやってとりあえず最低限の状態でビルドできるようになりました。

設定ファイルなど、上書きするものは本家で変更されたときに追従できませんが、まあなんとかなるでしょう。

そこら辺の処理はまた後で考えることにします。

## Error的な
ちゃんとビルドできているので、良いのですが、リポジトリをコピってるからか、リポジトリじゃないって怒られるんですよね。。。

どうにかならんかなぁ。。。

```
jekyll-jekyll-1  | ========================================
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | Configuration file: /usr/src/jekyll/_config.yml
jekyll-jekyll-1  |  Theme Config file: /usr/src/jekyll/_config.yml
jekyll-jekyll-1  |             Source: /usr/src/jekyll
jekyll-jekyll-1  |        Destination: /usr/local/app
jekyll-jekyll-1  |  Incremental build: disabled. Enable with --incremental
jekyll-jekyll-1  |       Generating...
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  | fatal: not a git repository (or any of the parent directories): .git
jekyll-jekyll-1  |                     done in 0.621 seconds.
jekyll-jekyll-1  |  Auto-regeneration: enabled for '/usr/src/jekyll'
```

## リポジトリ
[JO3QMA/docker-jekyllのchirpyブランチ](https://github.com/JO3QMA/docker-jekyll/tree/chirpy)に置いています。

このブランチ、将来的に自分専用のセットアップされる気がするのですが、まあ使う人なんて居ないので問題はないでしょう。

## その他
とりあえず、リポジトリはGitHubに置くことにしました。10GB超えたときに考えることにします。

ホスティングサービスとビルド周りはどうにかしたいですが、しばらく自宅サーバーでどうにかすることにします。