---
title: Macでお手軽にSQL練習環境を構築する方法【MySQL/Sequel Ace/Docker】
tags:
  - Docker
private: false
updated_at: '2025-08-31T21:46:42+09:00'
id: f2672da285c3ffc70e36
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
SQLの練習をするにあたって、Dockerを活用しながらお手軽に練習環境を作成することができたので、共有したいと思います！

「SQLの練習したいけど、環境構築どうすればいいの？」

「自分のPCに直接MySQLをインストールして汚したくない...」

「Dockerをまずは使ってみたいけど、よくわからない...」

そんな方に読んでいただけると嬉しいです🙌

![mysql-docker-sequelace.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/0e8df686-f773-4d39-8ec1-0ef862975c54.png)

# 目次
- [はじめに](#はじめに)
- [目次](#目次)
- [前提条件](#前提条件)
- [環境構築の手順](#環境構築の手順)
  - [Step1. プロジェクトディレクトリの作成](#step1-プロジェクトディレクトリの作成)
  - [Step2. `compose.yml`ファイルの編集](#step2-composeymlファイルの編集)
  - [Step3. Dockerコンテナの起動](#step3-dockerコンテナの起動)
  - [Step4. Sequel AceでMySQLに接続](#step4-sequel-aceでmysqlに接続)
  - [Step5. コンテナの停止と削除](#step5-コンテナの停止と削除)
- [終わりに](#終わりに)
- [参考記事](#参考記事)

# 前提条件
- 対象読者
  - SQLを学び始めた初心者の方
  - Macを利用している方
  - Dockerを少しでも触ったことがある方

- 準備するもの
  - [Docker Desktop](https://www.docker.com/ja-jp/products/docker-desktop/)
  - [Sequel Ace](https://apps.apple.com/jp/app/sequel-ace/id1518036000?mt=12)

:::note info
**Sequel Aceとは？**
- MySQLやMariaDBに接続してデータベースの操作ができるMac用のGUIクライアントアプリケーション
- 無料で利用でき、ターミナルでコマンドを打たなくても画面上から直感的にデータベースの操作が可能
- 今回の場合は、SQL実行とその実行結果が分かりやすいため採用
![スクリーンショット 2025-08-31 14.37.30.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/7a407b40-1e5c-4876-b59f-eddb637c0892.png)

:::

# 環境構築の手順
## Step1. プロジェクトディレクトリの作成
まずは、ターミナルを開いて、SQLの練習用ディレクトリを作成し、その中に`compose.yml`ファイルを作成します。
```bash
# ディレクトリの作成
mkdir sql-practice
# 作成したディレクトリに移動
cd sql-practice
# compose.ymlファイルの作成
touch compose.yml
# .envファイルの作成
touch .env
```

## Step2. `compose.yml`ファイルの編集
次に、`compose.yml`ファイルを編集します。以下のコードをコピペしてください。
```yml
services:
  db:
    image: mysql:8.0                                # イメージの指定
    container_name: mysql-container                 # コンテナ名の指定
    platform: linux/x86_64                          # Apple Silicon (M1/M2/M3) Macで安定動作させるための設定
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}   # .envファイルから読み込む
      MYSQL_DATABASE: ${MYSQL_DATABASE}             # .envファイルから読み込む
    ports:
      - "3306:3306"                                 # ホストのポート3306をコンテナのポート3306にマッピング
    volumes:
      - mysql-data:/var/lib/mysql                  # データ永続化のためのボリュームマウント

volumes:
  mysql-data:                                      # ボリュームの定義

```

続いて、`.env`ファイルを編集します。以下のコードをコピペしてください。
```env
# MySQLのrootユーザーのパスワード
MYSQL_ROOT_PASSWORD=sql_practice_password
# MySQLのデータベース名
MYSQL_DATABASE=sql_practice
```

## Step3. Dockerコンテナの起動
`compose.yml`ファイルと`.env`ファイルの編集が完了したら、作業用ディレクトリにて以下のコマンドを実行してDockerコンテナを起動します。

```bash
docker compose up -d
```

:::note info
**-dオプションとは？**
- detached（デタッチド）モードでコンテナを起動するためのオプション
- バックグラウンドで実行されるため、コンテナのログがターミナルに表示されず、ターミナルを他の作業に使用できる
- `-d`オプションを付けない場合、コンテナのログがターミナルに表示され続け、`Ctrl+C`を押すまでターミナルを操作できない
:::

コンテナが正常に起動しているか確認するには、以下のコマンドを実行します。
```bash
docker compose ps
```

:::note info
**コンテナの状態確認について**
- `Status`列が`running`または`Up`となっていれば、コンテナは正常に起動しています
- 以下のような出力が表示されれば成功です：
```
NAME              STATUS              PORTS
mysql-container   Up 2 minutes        0.0.0.0:3306->3306/tcp
```
- `Status`が`Exit`や`Error`の場合は、コンテナの起動に問題が発生している可能性があります
:::

## Step4. Sequel AceでMySQLに接続
コンテナが正常に起動したら、Sequel Aceを開いて、以下の情報を入力してMySQLに接続します。
- Host: `127.0.0.1`
- Username: `root`
- Password: `sql_practice_password`（`.env`ファイルで定義したもの）
- Database: `sql_practice`（`.env`ファイルで定義したもの）

![スクリーンショット 2025-06-21 19.50.52.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/61a56ff7-2bb6-4c87-8b6f-10415cc01412.png)

右下の「Test connection」をクリックして接続が成功すれば、「Add to Favorites」でお気に入りに追加しておくと、次回から簡単に接続できます。

青いボタンの「Connect」をクリックして接続します。接続に成功すると、以下のような画面が表示されます。

接続後は、以下の手順でSQLを実行できます🧑‍💻
1. データベースから`sql_practice`を選択
2. 上部のタブで「クエリ」を選択
3. SQLを入力して実行

これでSQLの練習環境が整いました！

![スクリーンショット 2025-06-21 19.54.52.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/18610e5f-9cb5-41b2-94f9-94cd1acd8158.png)

## Step5. コンテナの停止と削除
SQLの練習が終わったら、以下のコマンドでコンテナを停止,削除します。
```bash
docker compose down
```

削除されていることを確認します。
```bash
docker compose ps
```
コンテナが表示されなければ、削除されています。

これで一連の流れが終了となります！お疲れ様でした🎉

# 終わりに
Dockerを使うことで、PCに直接MySQLをインストールせずにSQLの練習環境を簡単に構築できたと思います！
最後まで読んでいただき、ありがとうございました😊

# 参考記事

https://apps.apple.com/jp/app/sequel-ace/id1518036000?mt=12

https://qiita.com/miya1221/items/1dee543d7f57c4f576cc
