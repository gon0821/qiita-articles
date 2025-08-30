---
title: 【Rails7 / 備忘録】リロードしないとJavaScriptが動かないんだけど、どした？エラー？
tags:
  - JavaScript
  - Rails
  - Turbo
  - Hotwire
  - Rails7
private: false
updated_at: '2023-10-19T10:13:52+09:00'
id: 3aaa4a252f7c936ca2bb
organization_url_name: null
slide: false
ignorePublish: false
---
# 開発環境
Ruby 3.2.2
Ruby on Rails 7.0.5

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/bd324611-8c8b-6cdd-bea9-459cedf99db3.png)


# はじめに

最近、Rails７でチャットアプリを開発しているのですが、チャットルームにページ遷移してメッセージを投稿しようとすると、投稿できない:fearful:
しかし、ページをリロードしてからメッセージを投稿すると、投稿できる。
サーバーサイド側を確認しにいっても間違ってそうなところはないし、、、、
調べていると、メッセージ送信時にイベントが発火するようにJavaScriptを組んでいるのにそれが発火していない模様。

エラー原因がわかり、同じような現象のエラー解決の記事の通り実行するも動作せず、、、
ググっていると以下のようなJavaScriptのコードを`Turbolinks`のイベントで囲む対策法が多かったです。
```javascript:JavaScript
document.addEventListener("turbolinks:load", function() {
  // ここにページが読み込まれたときに実行するコードを書く
});
```


どうやらRails7より`Turbolinks`に代わって使用されている`Turbo Drive`が関係していることが分かったのでそれについてまとめていきます:writing_hand:

`Turbolinks`については以下の記事で簡単にまとめているのでよかったらご参照ください。

https://qiita.com/gon0821/items/8e75f8743812bd9b9994


# Turbo Driveとは
基本的な機能は`Turbolinks`と同様で、ページ全体を再読み込みするのではなく、必要な部分だけを更新することで、ページ遷移を高速化します。

Rails7では、`Turbolinks`に代わりデフォルトで`turbo-rails`というgemが用意されており、このgemによって`Turbo Drive`が使えるようになっています。
```:Gemfile（Rails7）
# Hotwire's SPA-like page accelerator [https://turbo.hotwired.dev]
gem "turbo-rails"
```

一方、Rails6までは以下のようにデフォルトで`Turbolinks`がGemfileに含まれており、`Turbokinks`によりページ遷移の高速化を行っていました。
```:Gemfile（Rails6）
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
```

# TurbolinksとTurbo Driveの違い
機能はほとんど同じですが、変更点の一つに<font color=red>**イベント名**</font>が変わったのが挙げられます。
`turbolinks:~~`であったのが`turbo:~~`に変わっています！

| Turbolinks| Turbo Drive | 内容 |
| ------- | ------- | ------- |
| turbolinks:load   | turbo:load | ページのロード後 |
| turbolinks:click | turbo:click | リンククリック時 |
| turbolinks:before-cache | turbo:before-cache | ページのキャッシュ保存前 |
| turbolinks:before-visit | turbo:before-visit | ページ訪問前 |
| turbolinks:before-render | turbo:before-render | ページのレンダリング前 |


<sub>参考：[「猫でもわかるHotwire入門 Turbo編」](https://zenn.dev/shita1112/books/cat-hotwire-turbo/viewer/event)より</sub>


# なぜ今までのエラー解決記事でエラー解決できなかったか？
- **結論**
`Turbo Drive`のイベント名でコードを書かないといけないのに、`Turbolinks`のイベント名でコードを書いていたから

- **解決法**
読み込ませたいJavaScriptのコードを以下の通り囲むことで、動作させることができました。
```JavaScript:
document.addEventListener("turbo:load", function() {
  // ページが完全に読み込まれたときに実行されるコード
});
```


# おわりに

時間はかかりましたが、バージョンの違いによって書かないといけないコードが違ってくる場合があることを学ぶことができました。
今後エラー出たときは、このような「内容自体はあっているけど、書き方の違い」も想定していきたいと思いました:wink:

# 参考文献

この記事にはお世話になりました:pray:

https://zenn.dev/shita1112/books/cat-hotwire-turbo
