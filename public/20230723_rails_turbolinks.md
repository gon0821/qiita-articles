---
title: 【Rails / 備忘録】Turbolinksとはざっくりと何ですか？注意すべき点は？
tags:
  - JavaScript
  - Rails
  - turbolinks
  - 初学者向け
  - Rails6
private: false
updated_at: '2023-10-19T10:14:24+09:00'
id: 8e75f8743812bd9b9994
organization_url_name: null
slide: false
ignorePublish: false
---
# 前提条件
この記事は、Rails６までの内容で以下のようにデフォルトで`Turbolinks`がGemfileに含まれていることを想定しています。
```:Gemfile（Rails6）
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
```

Rails7では、`Turbolinks`に代わりデフォルトで`turbo-rails`というgemが用意されており、以下の記事内容は当てはまらないと思われますのでご注意ください。
```:Gemfile（Rails7）
# Hotwire's SPA-like page accelerator [https://turbo.hotwired.dev]
gem "turbo-rails"
```

# はじめに

最近、Railsでチャットアプリを開発しているのですが、開発中にエラーが出て原因を調べていたら、どうやら`Turbolinks`というものがエラーに関わっていることが分かりましたので、`Turbolinks`についてまとめていきたいと思います:writing_hand:

![undraw_the_search_s0xf.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/c62f643c-f7af-c9d1-0737-646ed95dd751.png)



# １. Turbolinksとは
Ruby on Railsフレームワークの一部として開発されたJavaScriptライブラリであり、利用しやすいようにGemとしてデフォルトで組み込まれています。そして、`Turbolinks`のおかげで画面遷移の高速化を実現することができます。

具体的に見ていきましょう:point_up:
通常、ユーザーが新しいページに遷移するとき、ブラウザはページ全体を再読み込みします。
つまり、HTML、CSS、JavaScriptなどのすべてのアセットをサーバーから再度ダウンロードするためページ遷移のたびに多くの時間とリソースを消費してしまいます。

そこで`Turbolinks`の登場です！
1. `Turbolinks`は、新しいページへのリンクをクリックすると、ブラウザにフルページの再読み込みを行わせずに、新しいページのHTMLをAjaxリクエストで取得します。
1. リクエストを受けたサーバーは、HTMLをレスポンスとして返します。
1. レスポンスとして返ってきたHTML要素にある、`body`タグの中身で現在のページの`body`タグの中身を置き換えます。`head`タグ内に含まれるJavaScriptやCSSが既に読み込まれているスクリプトやスタイルシートは再度読み込まれません。

こうすることで、ページ全体を再読み込みするのではなく、変更が必要な部分だけを更新するので画面遷移が高速になり、パフォーマンスを向上することができます。



# 2. Turbolinksを使用する際の注意点
一見、「`Turbolinks`めっちゃいいやつじゃん」と思うのですが、使用する際には注意が必要です。
`Turbolinks`はページ全体の再読み込みを行わないため、ページロード時に実行されるはずのJavaScriptが実行されない場合があります。そのため、JavaScriptを適切に動作させるためには、`Turbolinks`専用のイベントに合わせてコードを調整し、既存のJavaScriptを正常に動作させます。

```javascript:イベント書き方例
document.addEventListener("turbolinks:load", function() {
  // ここにページが読み込まれたときに実行するコードを書く
});

```


# 3. Turbolinksのライフサイクルイベント
`Turbolinks`専用のイベントの一例について以下に示します。

| イベント | 内容 |
| ------- | ------- |
| turbolinks:load   |  Turbolinksが新しいページを読み込み終わったときに発火します。これは最も頻繁に使用されるイベントで、新しいページが完全に読み込まれたときにコードを実行するために使用します。   |
| turbolinks:click | ユーザーがTurbolinksによって管理されるリンクをクリックしたときに発火します。 |
| turbolinks:before-cache | Turbolinksが現在のページをキャッシュに保存する前に発火します。 |
| turbolinks:before-visit |Turbolinksがページを訪問する直前に発火します。|
| turbolinks:request-start | Turbolinksが新しいページのHTMLをフェッチし始めたときに発火します。 |
| turbolinks:before-render | Turbolinksが新しい<body>要素をDOMに挿入する前に発火します。 |



# 4. おわりに

`Turbolinks`についてざっくりと理解できたので、次回はRails7よりTurbolinksの機能を`Turbo Drive`というもので実現しているらしいのでそちらについて調べたいと思います:seedling:

# 5. 参考文献

https://qiita.com/morrr/items/54f4be21032a45fd4fe9

https://qiita.com/zenfumi/items/a92c0385d361e04fb014

https://techtechmedia.com/turbolinks-rails/

