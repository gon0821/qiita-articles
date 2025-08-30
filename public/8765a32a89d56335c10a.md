---
title: 【Docker初心者】Dockerの基本操作について　Part２（ポート公開、コマンド実行、削除等）
tags:
  - Docker
  - 初学者
private: false
updated_at: '2023-05-31T19:13:36+09:00'
id: 8765a32a89d56335c10a
organization_url_name: null
slide: false
ignorePublish: false
---
# １.はじめに
最近、山浦清透さんが運営されているプログラミング学習サービス「[独学エンジニア](https://dokugaku-engineer.com/)」にてDockerについて学び始めました(^o^)
講義内容とほとんど同じになりますが、備忘録も兼ねて自分なりにまとめていきたいと思います。
今回の内容は前回投稿した記事の続きになりますので、前回の内容を見たい方がいらっしゃいましたら[Part1](https://qiita.com/gon0821/items/2ab56df440e057f9d1b7)よりご覧ください。
![docker_logo_icon.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/a84c3e21-c11a-3de6-aac8-378d66daa685.png)

（内容と関係ないけどDockerのクジラのアイコン、健気にコンテナ運んでるの可愛くて結構好き、、、）

# ２.Dockerの基本操作

# （１） コンテナのポートを公開する
コンテナ起動後、ブラウザからコンテナにリクエストを送り、Webページを開きたいとします。
しかし、コンテナは<font color=red>外部からの影響受けずに１つの独立した仮想マシンとして振る舞えるようにしているため、ネットワークは独立し、外部に対して閉じています。</font>このままではリクエストをコンテナが受け取れません(-_-;)

![コンテナ(3).jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/a7d8941a-cf90-a7e1-0b50-ac96c1b83853.jpeg)


そこで外部からコンテナと通信するには、**コンテナのポート番号をホストに対して公開し、ホストへの特定ポート番号への通信をコンテナの公開したポート番号へマッピングすること**が解決法となります。

コマンドは「docker container run」コマンド実行時に、**「-p」オプション**を付けます。
（余談ですが、最初の頃は「-p」がポート（port）の頭文字だと思っていましたが、公開する（publish）の頭文字でした、、、！！）

```zsh:コンテナのポートをホストに公開する
# ホストのポート番号9999とコンテナのポート番号８０をマッピングする
$ docker container run -p 9999:80 myapache:latest
```

:::note info
ポート番号とは？
一言で表すと、「どのアプリケーション宛の通信なのかを指定する番号」
よく例えられるのは、マンションの部屋番号ですね！
マンションの住所だけでは郵便物をどこに届けたらいいか分からないけど、部屋番号まであれば届けられる(^^)
下記は代表的なポート番号です。
| ポート番号 | アプリケーション・サービス |
|:-:|:-:|
| ポート８０ |　HTTP通信（クライアントとサーバー間で使用される通信） |
| ポート４４３  |  HTTPS通信（HTTP通信を暗号化した通信） |
|  ポート１４３ | IMAP通信（メールサーバ上で電子メールを閲覧する）  |　

:::

# （２） ログを確認する
コンテナ内に記録されているログの確認方法です。
コンテナ周りで問題が起きた場合にログを確認することがあるので、その際に使用します。

```zsh:docker container logsコマンド
# 書式：　docker container logs [オプション] コンテナ
$ docker container logs apache
```
# （３） コンテナでコマンドを実行する
コンテナ内でコマンドを実行したいときに以下のコマンドを使用します。
bash等のシェルを起動させて、コンテナから抜けずにコンテナ内で操作を続けたい場合は、**「-it」オプション**を付けることで可能になります。

```zsh:docker container execコマンド
# 書式：　docker container exec [オプション] コンテナ コマンド [引数]
$ docker container exec apache ls /

# -itオプション
# 標準出力とターミナルをアタッチする（interactive,tty）　→　シェルを終了させない
$ docker container exec -it apache /bin/bash
```

# （４） コンテナを停止・削除する
起動中のコンテナはいきなり削除できないため、**コンテナの停止 → コンテナの削除**
と段階踏んで、削除する必要があります。

![コンテナ(4).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/ded6f6f6-c99a-b917-6cb8-2ef1895fb887.png)


これは「間違えてコンテナを消してしまった」などのヒューマンエラーを防止するためにあるのでしょう(^o^)
しかし、起動中のコンテナを強制削除するオプション「-f」も用意されています。

* **コンテナを停止する**
```zsh:docker container stopコマンド
# 書式：　docker container stop [オプション] コンテナ
$ docker container stop apache 
```
* **コンテナを削除する**
```zsh:docker container rmコマンド
# 書式：　docker container rm [オプション] コンテナ
$ docker container rm apache 

# 起動中のコンテナを強制削除する
$ docker container rm -f apache
```
# （５） イメージを削除する
イメージやコンテナはずっと残っているとPCの容量を圧迫するので、必要がなくなれば削除するようにしましょう。


```zsh:docker image rmコマンド
# 書式：　docker image rm [オプション] イメージ
$ docker image rm practice/apache 
```

# ３．おわりに
最後まで見ていただき、ありがとうございました！
自分でイメージ図を作成しながらまとめることで、視覚的に理解でき、より理解が深まったと思います(^^)
次回は、イメージの基となる「Dockerfileの書き方について」まとめることができればと思います！


# ４.参考資料
* [独学エンジニア（Lesson34参照）](https://dokugaku-engineer.com/)
独学エンジニアとても分かりやすく、記事の内容は講義内容を参考に作成しております。
月額約1,000円で非常にオススメです！
