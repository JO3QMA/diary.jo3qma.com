---
layout: post
category: "ブログ"
tags: ["Jekyll", "Docker"]
title: "自分用のjekyllコンテナ"
---

# Jekyllコンテナを作った。
[公式のjekyll](https://hub.docker.com/r/jekyll/jekyll/)イメージ、なんかwebrick入ってないって怒られるのが不服だったので、自分用に作りました。

最近Dockerfile書いてなかったので良いリハビリになりました。

serveもbuildもその他コマンドもだいたい使えます。

# モノ
## Dockerfile
```Dockerfile
FROM ruby:3.1.2-bullseye

# パッケージ関連
RUN echo "リポジトリをrikenに変更します。" \
&&  echo "deb http://ftp.riken.jp/pub/Linux/debian/debian bullseye main contrib non-free" > /etc/apt/sources.list \
&&  echo "deb http://security.debian.org/debian-security bullseye-security main" >> /etc/apt/sources.list \
&&  apt update \
&&  apt upgrade -y \
&&  apt install -y \
      build-essential \
      git \
      sudo \
&&  echo "update完了しました。"

# 実行ユーザーを作成
ENV USER_NAME=jekyll
RUN echo "実行ユーザーを作成します。" \
&&  echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/${USER_NAME} \
&&  chmod u+s /usr/sbin/useradd \
&&  chmod u+s /usr/sbin/groupadd

RUN  gem update \
&&  gem install jekyll bundler

ADD entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh \
&&  mkdir /usr/src/app -p \
&&  mkdir /usr/local/app -p
#WORKDIR /usr/src/app

ENTRYPOINT ["/bin/bash", "/usr/local/bin/entrypoint.sh"]
EXPOSE 4000
```

## entrypoint.sh
```shell
#!/bin/bash
USER_ID=$(id -u)
GROUP_ID=$(id -g)

# 初期化
SRC_DIR=/usr/src/app
DEST_DIR=/usr/local/app

# グループを作成する
if [ x"$GROUP_ID" != x"0" ]; then
    groupadd -g $GROUP_ID $USER_NAME
fi

# ユーザを作成する
if [ x"$USER_ID" != x"0" ]; then
    useradd -d /home/$USER_NAME -m -s /bin/bash -u $USER_ID -g $GROUP_ID $USER_NAME
fi

# パーミッションを元に戻す
sudo chmod u-s /usr/sbin/useradd
sudo chmod u-s /usr/sbin/groupadd

# ディレクトリ作成
mkdir -p $SRC_DIR
mkdir -p $DEST_DIR
# パーミッション変更
sudo chown $USER_NAME:$USER_NAME $SRC_DIR
sudo chown $USER_NAME:$USER_NAME $DEST_DIR

exec $@

# 初期化
SRC_DIR=/usr/src/app
DEST_DIR=/usr/local/app

# ディレクトリ作成
mkdir -p $SRC_DIR
mkdir -p $DEST_DIR

# 設定値表示
echo "========================================"
echo "Source Dir      : ${SRC_DIR}"
echo "Target Dir      : ${DEST_DIR}"
echo "Jekyll Mode     : ${JEKYLL_MODE}"
echo "jekyll ARGS     : ${JEKYLL_ARGS}"
echo "jekyll NEW BLANK: ${JEKYLL_NEW_BLANK}"
echo "========================================"

cd $SRC_DIR

# $SRC_DIRが空ならば初期テンプレート作成
if [ -z "$(ls $SRC_DIR)" ]; then
  ${JEKYLL_NEW_BLANK:=false} #JEKYLL_NEW_BLANKが未定義の場合、falseを代入。
  if   [ $JEKYLL_NEW_BLANK = false ]; then
    echo "jekyll newを実行しました"
    jekyll new $SRC_DIR
  elif [ $JEKYLL_NEW_BLANK = true ]; then
    echo "jekyll new --blankを実行しました"
    jekyll new $SRC_DIR --blank
  else
    echo "JEKYLL_NEW_BLANKにBool以外の値が代入されています。"
    exit 1
  fi    
fi

# Gemfileにwebrickがない場合追加
echo "GemfileにWebrickが記述されていない場合、追加します。"
grep -q "webrick" "${SRC_DIR}/Gemfile"
if [ $? -eq 0 ]; then
  echo "webrickはすでに入っています。"
elif [ $? -eq 1 ]; then
  echo $PWD
  echo "webrickを追加しました。"
  bundle add webrick
else
  echo "エラーが発生しました。 Exit Code: ${?}"
  exit 1
fi

# bundle
echo "Bundle installをします"
/usr/local/bundle/bin/bundle install

echo "========================================"

# Jekyll 起動
if   [ ${JEKYLL_MODE} = "serve"     ]; then
  /usr/local/bundle/bin/bundle exec jekyll serve     -s ${SRC_DIR} ${JEKYLL_ARGS} -d ${DEST_DIR} --host=0.0.0.0 --watch
elif [ ${JEKYLL_MODE} = "build"     ]; then
  /usr/local/bundle/bin/bundle exec jekyll build     -s ${SRC_DIR} ${JEKYLL_ARGS} -d ${DEST_DIR}
elif [ ${JEKYLL_MODE} = "doctor"    ]; then
  /usr/local/bundle/bin/bundle exec jekyll doctor    -s ${SRC_DIR} ${JEKYLL_ARGS} -d ${DEST_DIR}
elif [ ${JEKYLL_MODE} = "clean"     ]; then
  /usr/local/bundle/bin/bundle exec jekyll clean     -s ${SRC_DIR} ${JEKYLL_ARGS} -d ${DEST_DIR}
elif [ ${JEKYLL_MODE} = "new-theme" ]; then
  /usr/local/bundle/bin/bundle exec jekyll new-theme -s ${SRC_DIR} ${JEKYLL_ARGS} -d ${DEST_DIR}
else
  # それ以外はエラーを返す
  echo "モードが不適切です。使用可能なモードは(serve|build|doctor|clean|new-theme)です。"
  echo "newは/usr/src/appが空の場合、自動的に実行されます。"
  exit 1
fi
```

## docker-compose.yml
```yaml
version: "3"
services:
  jekyll:
    build: ./build
    tty: true
    environment:
      TZ: Asia/Tokyo
      JEKYLL_MODE: "serve"
      JEKYLL_ARGS: ""
      JEKYLL_NEW_BLANK: false
    volumes:
      - ./app:/usr/src/app
    user: "1000:1000"
    ports:
      - 4000:4000
```

jekyllを起動するところなど、色々と気に食わない処理がありますが、とりあえず動いているので今のところ良いです。

`Dockerfile`と`entrypoint.sh`をbuildディレクトリに入れ、docker-compose.ymlで適当に設定をし、
```bash
$ docker-compose build; docker-compose up -d
```
でもしてやると4000ポートで起動してくれます。

コンテナを起動する際に
- JEKYLL_MODE
- JEKYLL_ARGS
- JEKYLL_NEW_BLANK

という環境変数を指定することができます。
### JEKYLL_MODE
`jekyll serve`や`jekyll build`などのモードを入れる場所です。

(`serve`\|`build`\|`doctor`\|`clean`\|`new-theme`)が入力可能です。

`jekyll new`に関しては、ソースディレクトリが空の場合、事前に実行されます。
### JEKYLL_ARGS
jekyllを起動する際のオプションです。

`-s`や`-d`などのオプションはすでに指定しています。その他のオプションを使用してください。｀

なお、serveに関しては`--host=0.0.0.0`及び`--watch`オプションはすでに指定されます。

### JEKYLL_NEW_BLANK
jekyllのソースディレクトリが空の場合、`jekyll new`が自動的に実行されますが、その際に`--blank`オプションを有効にするか否かのオプションです。


# その他
もう少し改良したい点があるので、気が済んだらGitHubにでも上げておきます。

このサイトを置くホスティングサービスどうしようかなぁ。。。

レンタルサーバーとかはこの性能でこの値段かぁ・・・みたいなのが多くてアレ。(自宅サーバーと比較するな)(性能気にしたくなくてSSGにしたんだろ)

無償のならばCloudFlare Pagesがいい感じに良さそうな気はする。

問題は、リポジトリ置き場かなぁ・・・。

今みたいな文章メインならそこまで肥大化しないと思うけど、画像使い始めるとGitHubの10GB制限に引っかかりそう。

画像だけ別のホスティングサービスに投げるってのもありだけど、それをすると管理の煩雑さが上がってしまう上に、画像ホスティングサービスがサービス終了した場合、泣きを見るハメになる・・・。