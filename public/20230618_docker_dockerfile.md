---
title: 【Docker初心者】Dockerfileの書き方について
tags:
  - Docker
  - 初心者向け
private: false
updated_at: '2023-06-18T11:06:04+09:00'
id: f9e3bcbb6cb01d4ef7fa
organization_url_name: null
slide: false
ignorePublish: false
---

# １.はじめに
最近、山浦清透さんが運営されているプログラミング学習サービス「[独学エンジニア](https://dokugaku-engineer.com/)」にてDockerについて学び始めました(^o^)
講義内容とほとんど同じになりますが、備忘録も兼ねて自分なりにまとめていきたいと思います。
今回の内容はDockerの基本操作を理解した上での記事となりますので、まだ分からないって方は以前投稿した記事、[「Docker基本操作Part1」](https://qiita.com/gon0821/items/2ab56df440e057f9d1b7)[「Docker基本操作Part2」](https://qiita.com/gon0821/items/8765a32a89d56335c10a)を軽くご一読ください。


![docker_logo_icon_169251.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/9c897510-4361-3e75-ca9c-d5a0f0799daa.png)

# ２.Dockerfileとは
Dockerfileとは、Dockerイメージを作成するためのテキストファイルです。
Dockerfile内には、基本となるOS、インストールする必要があるソフトウェア、コピーするファイル・ディレクトリ、開くポート、実行するコマンドなど、新しいDockerイメージを作成するために必要な指示が含まれています。
Dockerfileの書き方としては、「FROM」,「RUN」,「CMD」などのインストラクションに引数を記述し、作成します。

![コンテナ(5).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/2075b2e1-73d9-67f7-d4c5-e5fa9b3209f4.png)


# ３．各インストラクションについて

# （１） FROM
「FROM」インストラクションはDockerfileの中で一番最初に記述されるべき命令で、新たに作成するDockerイメージのベースとなるイメージを指定します。
また、ベースイメージはDockerHubを参考にします。
```Dockerfile:FROM：インストラクション
# 書式：　FROM [イメージ] [タグ]
$ FROM　ubuntu:22.10
```
「FROM」インストラクションの後にはイメージ名とタグを指定します。
イメージ名はそのイメージが何を表しているのか（例えば、Ubuntu,Node.js,Rubyなど）を示し、タグはそのイメージのバージョンを表します。

# （２） RUN
「RUN」インストラクションはDockerイメージのビルド時にシェルコマンドを実行するための命令です。
この命令を使用することで、イメージにソフトウェアをインストールしたり、セットアップの手順を実行したりすることができます。
```Dockerfile:RUN：インストラクション
# 書式：　RUN [コマンド] 
$ RUN apt update \
    && apt install -y apache2
```
「RUN」インストラクションは実行ごとに新しいレイヤを作成してその上でコマンドを実行するため、多くの「RUN」を持つDockerfileは多くのレイヤから成るイメージを作成し、イメージのサイズが大きくなります。しかし、レイヤ数が多いと、PCの容量が圧迫されたり、イメージのビルドに時間がかかったりするため、レイヤ数は最小限にすることが望ましいです。
このため、複数のコマンドを一度にまとめて実行するときは「&&」で繋ぎ、「RUN」を減らし、複数行になるときは「\」（バックスラッシュ）で繋げることにより見やすくしましょう。

# （３） CMD
「CMD」インストラクションは、Dockerコンテナが実行されたときにデフォルトで実行するコマンドを定義します。

```Dockerfile:CMD：インストラクション
# 書式：　CMD ["実行ファイル", "パラメータ1","パラメータ2"] 
$ CMD ["apachectl","-D","FOREGROUND"]
```
CMDの指定方法にはexec形式とshell形式の２種類あり、基本的にはexec形式を使います。
[]で囲み、一つずつパラメータをダブルクオーテーションで囲むことに注意しましょう。

:::note info
RUNとCMDの違いとは？
インストラクションで受け取る引数にあるコマンドを実行する点では似ている「RUN」と「CMD」ですが、大きな違いは「RUNはレイヤを作る」、「CMDはレイヤを作らない」ことです。
レイヤは再生可能であり、Dockerは一度ビルドされたレイヤをキャッシュして再利用することができるため、ソフトウェアのインストールやファイルのコピーなどイメージの構築に必要な手順を「RUN」で指示することによりベースイメージにキャッシュさせ覚えさせることで、何度もダウンロードせず済みます。
:::

# （４） COPY
「COPY」インストラクションは、ホスト（ローカル）のファイルやディレクトリをDockerイメージにコピーします。
具体的な使い方としては、ソースコードや設定ファイルはローカルで作成して、COPYを使ってDockerイメージにコピーして使用します。

```Dockerfile:COPY：インストラクション
# 書式：　COPY [コピー元][コピー先] 
①ファイルをファイルに
$ COPY　index.html /mydir/index.html 
②ファイルをディレクトリ以下に
$ COPY　index.html　/mydir/ 
③ディレクトリをディレクトリに            
$ COPY src/ /mydir/
```

# （５） ENV
「ENV」インストラクションは、Dockerfile内で環境変数を設定するために使用されます。
設定された環境変数は、その後のDockerfileの中で使用することができ、また生成されたDockerイメージから作成される全てのコンテナで利用可能です。

```Dockerfile:ENV：インストラクション
# 書式：　ENV [キー]　＝　[値] 
$ ENV MYSQL_USER=admin
$ ENV MYSQL_PASSWORD=password
```


# （６） WORKDIR
「WORKDIR」インストラクションは、コマンドを実行する作業ディレクトリを指定します。

```Dockerfile:WORKDIR：インストラクション
# 書式：　WORKDIR [ディレクトリのパス] 
$ WORKDIR /app
```
「WORKDIR」インストラクションを使用することで、Dockerfileが読みやすくなり、絶対パスを何度も書く手間が省くこともできるので、適切に使っていきましょう。


# ４．おわりに
最後まで見ていただき、ありがとうございました！
自分で作成しながらまとめることで、より理解が深まったと思います(^^)
次回は、「Docker Composeについて」まとめることができればと思います！


# ５.参考資料
* [独学エンジニア（Lesson35参照）](https://dokugaku-engineer.com/)
独学エンジニアとても分かりやすく、記事の内容は講義内容を参考に作成しております。
月額約1,000円で非常にオススメです！

* [米国AI開発者がゼロから教えるDocker講座](https://www.udemy.com/course/aidocker/)
