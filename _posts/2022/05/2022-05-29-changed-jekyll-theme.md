---
layout: post
title: "ブログのテーマを変更した。"
---

# Chirpyというテーマにした
昨日、半日ぐらいつぶしてJekyllにテーマを入れる方法及び、いい感じのテーマを探してました。

できるだけシンプルなものを探してて、最終的に[Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)というテーマにたどり着きました。

ダークテーマを選択でき、レスポンシブ対応なので気に入っています。

初期設定ではpermalinkが`/posts/title/`形式で気に入らなかったので、その部分だけ書き換えています。
```diff
- permalink: /posts/:title/
+ permalink: /posts/:year/:month/:day/:title/
```
## おうちの外からでも更新できるように対応
暫定的対処ですが、Web版VSCodeの[code-server](https://github.com/coder/code-server)も自宅サーバーで一緒に建てているので、ブログのディレクトリをマウントしてやって、更新できるようにもしました。

どうせ、Markdownしか書かないので、テキストエディタでいいのでこれが最善策かなって思います。

あとは、Gitリポジトリ周りとホスティングサービス探すことかなあ・・・。