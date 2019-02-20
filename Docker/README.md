# Dockerについて

「コンテナ型の仮想環境」を作れるやつ（コンテナ型の仮想環境というのが何なのかはよくわかっていない）。

VirtualBoxなどは「ハイパーバイザー」（など？）を使って仮想マシンを作成し、その上でゲストOSを動かす（ハードウェアのエミュレートが必要？）。  
ゲストOSを動かす必要があるため、リソースの消費量が多い。

コンテナ型の仮想環境は、ホストOSのカーネルを利用するから軽量・高速

`Dockerfile`を用いて`Infrastructure as Code`を実現できる。

`Dockerfile`をビルドすると`Dockerイメージ`ができ、`Dockerイメージ`を起動すると`Dockerコンテナー`として動く。

## 嬉しいこと

### `Infrastructure as Code`が実現できる。

インフラ構成などをコードとして記述しておけるため、属人化が防げたり環境の再現性を高められたりする。

### Dockerfileが公開されたOSSが多い

様々なOSSがDockerfileを公開しており、あるOSSを試す際にDockerで簡単に動かすことができる。そのOSSのインストールすら必要ない。

## 使い方

インストール方法は省略

### `Dockerfile`から`Dockerイメージ`を作る

動作イメージを掴むための簡単な`Dockerfile`。これを適当なディレクトリに保存する。

```Dockerfile
FROM alpine:latest # alpineというDockerイメージをもとにイメージを作る

CMD ["echo", "Hello, Docker World!!"] # ダブルクォートじゃないとダメ
```

`Dockerfile`を保存したディレクトリでターミナルを開き、以下のコマンドを実行する（MacやWindowsの場合は`sudo`がいらない)ことで`hello-docker`という名前のイメージを作成することができる。イメージ名は小文字しか使えない。

```bash
sudo docker build -t hello-docker .
```

### `Dockerイメージ`を起動する

以下のコマンドで、ローカルにあるDockerイメージを表示できる。

```bash
sudo docker image ls
```

`hello-docker`イメージあると思うので、以下のコマンドで`hello-docker`イメージを起動する。

```bash
sudo docker run --rm -it hello-docker
```

`Hello, Docker World!!`が表示される。

`--rm`オプションは、実行が終わったコンテナを自動で削除するためのものらしい。

`-it`を付けておくと、コンテナ内の標準入出力とターミナルを繋げるようなオプションらしい(ちなみに`-i`と`-t`を同時に指定してるのと同じ意味)。

## もう少し実用的な`Dockerfile`

コンテナを起動したらメッセージを表示するだけの`Dockerfile`では面白くないので、もう少し実用的な何かをやってみる。

`express`を使ったWebサーバーを建ててみる。

[express公式](https://expressjs.com/ja/starter/hello-world.html)のサンプルソースを使う。

Dockerfileは以下の通り。

```Dockerfile
FROM alpine:latest

# 作業ディレクトリを指定する
WORKDIR /workspace

# コンテナ内で3000ポートを使用することを宣言
EXPOSE 3000

# ホストに存在するファイルをコンテナ内にコピーする
COPY package.json .
COPY package-lock.json .
COPY index.js .

# node.jsとnpmをインストールしたあとにexpressとその依存ライブラリをインストールする
RUN apk add --no-cache nodejs npm && npm install

# `docker run`時にnpm startを実行する
CMD ["npm", "start"]
```

このDockerfileを用意した状態で`sudo docker build -t express-example`とするとイメージが作成できる。

そして以下のコマンドで作成したイメージを実行する。

```bash
sudo docker run --rm -p 3000:3000 express-example
```

ターミナル上に`Example app listening on port 3000!`と表示されており、その状態でブラウザで`localhost:3000`にアクセスすれば`Hello World!`が表示されるはず。

ターミナル上で`^C`(コントロール+C)を押せばコンテナのプロセスが終了する。

また、ターミナル上ではなくバックグラウンド(?)で動かしたい場合は以下のように`-d`を付ける。

```bash
sudo docker run --rm -p 3000:3000 -d express-example
```

## イメージ一覧とコンテナ一覧、そして削除方法

### イメージの一覧

```bash
sudo docker image ls
```

### コンテナの一覧

```bash
sudo docker container ls
```

### イメージの削除

```bash
sudo docker rmi (イメージ名 or イメージID)
```

### コンテナの削除

```bash
sudo docker rm (コンテナID)
```

### コンテナの一括削除

```bash
sudo docker rm `sudo docker container ls -aq`
```
