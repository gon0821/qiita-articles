---
title: 【Docker初心者】Dockerの基本操作について　Part１（buildコマンド、runコマンド等）
tags:
  - 初心者
  - Docker
private: false
updated_at: '2024-12-16T12:19:51+09:00'
id: 2ab56df440e057f9d1b7
organization_url_name: null
slide: false
ignorePublish: false
---
# １.はじめに
最近、山浦清透さんが運営されているプログラミング学習サービス「独学エンジニア」にてDockerについて学び始めましたので、備忘録も兼ねて自分なりにまとめていきたいと思います。

# ２.Dockerとは
アプリケーションとその依存関係にあるOS・ライブラリを軽量で移植性のあるコンテナと呼ばれる箱に入れて、あたかも違うコンピュータのように扱えるものです。

![コンテナ.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/c2f7a205-c30d-bb26-9445-2ff622d50097.jpeg)




# ３.Dockerのメリット
メリットについては多数あるのですが、いくつか紹介します。

* **環境の一貫性**
開発、テスト、本番環境で同じDockerイメージを使用することで、環境の違いによる問題を軽減できます。

* **セットアップの容易さ**
Dockerイメージを使用することで、開発者はOSやインストール方法の違いを気にせず、簡単にアプリケーションの環境を構築できます。

* **移植性**
Dockerコンテナは異なるマシンやクラウドプラットフォームで簡単に実行できるため、デプロイメントの柔軟性が向上します。

* **チームの協調性**
Dockerを使用することで、開発者間で環境設定の共有が容易になり、チーム全体の生産性が向上します。

* **インフラストラクチャ効率**
Dockerコンテナは軽量であり、同じホスト上で複数のコンテナを実行できるため、リソース使用効率が向上し、インフラストラクチャのコストが削減されます。

# ４.Dockerの基本操作
# （1） イメージを取得・ビルドする
コンテナの設計書となるイメージには２通りの作成方法があります。
* **Dockerレジストリより取得する**
```zsh:コマンド
# DockerHubから任意のDockerイメージ(imagename)を取得する場合
$ docker image pull imagename
```

* **Dockerfileをビルドする**
```zsh:コマンド
# Dockerfileがカレントディレクトリにある場合
$ docker image build .

#　タグを付ける場合（-tオプション）
#　タグの書式：　　名前空間/イメージ名:タグ名
$ docker image build -t test/apache:latest .

#　ファイル名を指定する場合（-fオプション）
$ docker image build -f Dockerfile-test .
```

# （2）イメージの一覧を確認する
```zsh:コマンド
# 現在のイメージの一覧を確認する
$ docker image ls
```

# （3） コンテナを生成・起動する


![コンテナ (2).jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/67f76153-8b58-1485-f24c-c9f4b3686d8d.jpeg)

コンテナの生成・起動には「run」コマンドを、
生成だけには「create」コマンドを、起動だけには「start」コマンドを用います。
```zsh:コマンド
# 書式：docker container run [オプション] イメージ [コマンド]
$ docker container run test/apache:latest

# コンテナをバックグランドで実行する（-dオプション）
$ docker container run　 -d test/apache:latest

# コンテナに名前を付ける(--nameオプション)
$ docker container run --name apache_test test/apache:latest

# 標準出力とターミナルをアタッチする（-itオプション）
$ docker container run　 -it test/apache:latest
```
「-it」オプションはinteractiveの-iオプションとttyの-tオプションの２つを繋げたもので、
コンテナ上で最初に実行されるプロセスがシェルの場合、「-it」オプションを付けることによりコンテナを終了させずに使用できるようになります。

 
# （4） コンテナの一覧を確認する

```zsh:コマンド
# 起動中のコンテナ一覧を確認する
$ docker container ls

# 全コンテナの一覧を確認する（-aオプション）
$ docker container ls　 -a
```

# 5.終わりに
Dockerの超基本的なことまとめただけなのですが、やはり自分でアウトプットするとモヤモヤしていた部分がスッキリしたので、積極的にQiita等にアウトプットしていきたいと思います！

[Part2](https://qiita.com/gon0821/items/8765a32a89d56335c10a)では、ポートを公開したり、コンテナ内でコマンドの実行等の基本操作についてまとめて行きたいと思います。

Dockerは使いこなせると環境構築等で本当に便利そうなので早くマスターしたいですね。
また、ポートフォリオにもDockerを使用して作成したいので引き続き学んでいきたいと思います(*^^*)

# ６.参考資料
* [独学エンジニア（Lesson33・34参照）](https://dokugaku-engineer.com/)
独学エンジニアとても分かりやすく、記事の内容は講義内容を参考に作成しております。
月額約1,000円でオススメです！
