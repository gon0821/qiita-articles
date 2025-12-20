---
title: 【Rails初学者】ActionViewのパーシャルについてざっくり理解する
tags:
  - Rails
  - actionview
  - 初学者
  - パーシャル
private: false
updated_at: '2025-12-20T20:07:56+09:00'
id: a314b16ff04a9e460add
organization_url_name: null
slide: false
ignorePublish: false
---
# ゴール
この記事では、RailsのActionViewにおけるパーシャルについて基本的な部分をざっくり理解することを目指します⭐️
パーシャルの役割や使い方、メリットについてイメージできる状態になることを目標とします🙋

![rails-partial.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/22a7d574-e80a-4cf8-9009-9f570c087a1c.png)

# パーシャルとは？
パーシャル（Partial）とは、RailsのActionViewにおいて、再利用可能なビューの一部を切り出して定義するための仕組みです！
パーシャルを使用することで、同じコードを複数のビューで繰り返し使用することができ、コードの重複を避け、保守性を向上させることができます⭕️
例えば、ユーザー情報の表示部分やナビゲーションバーなど、複数のページで共通して使用される部分をパーシャルとして切り出すことができます。

**パーシャルがなかったとき😭**

![template-without-partial.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/c31aa1d8-e851-487d-8f6b-e5ca5f1dc11c.png)

ヘッダーとフッターは同じ内容にもかかわらず、各テンプレートでコードを記述する必要があり、コードの可読性や保守性で問題があります。
- 新しいテンプレートを作成する際に「またヘッダーやフッターの同じコードを書かなければあかん😰」などの手間が増える。
- コピペすればいい話ですが、作業が増える分ミスが起きる可能性も高まりますよね。。。
- ヘッダーの改修依頼がきた場合はどうでしょうか？テンプレートが100ページあったとしたら、100箇所修正しないといけません😱「だるすぎる😇」

**パーシャルがあったとき☺️**

![rails-parial.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/b5b39347-6e6e-454e-82ff-7ebf761cce28.png)

ヘッダーとフッターをパーシャルとして切り出すことで、各テンプレートではパーシャルを呼び出すだけで済み、コードの重複を避けることができます。
- 新しいテンプレートを作成する際に「ヘッダーやフッターはパーシャルを呼び出すだけでOK🙆‍♂️」と手間が減る。
- ヘッダーの改修依頼がきた場合も「パーシャルファイルを1箇所修正すればOK👍」と作業量も少ない

# パーシャルの使い方
パーシャルを作成するには、ビューのファイル名の先頭にアンダースコア（`_`）を付けて保存します！例えば、`_header.html.erb`や`_footer.html.erb`などです。
パーシャルを呼び出すには、`render`メソッドを使用します。

```erb
<!-- ヘッダー -->
<%= render 'header' %>
<!-- メインのコンテンツ -->
<h1>Welcome to My Website</h1>
<p>This is the main content of the page.</p>
<!-- 共通のコンテンツ -->
<%= render 'form' %>
<!-- フッター -->
<%= render 'footer' %>
```

上記の例では、`header`と`footer`、`form`というパーシャルを呼び出しています。Railsは自動的にアンダースコア付きのファイル名を探してレンダリングします。

:::note info
**`partial:`オプションについて**
- `render`メソッドでパーシャルを呼び出す際に、`partial:`オプションを使用して明示的にパーシャルを指定することもできます。
- ただし、`partial:`オプションは省略可能であり、単に`<%= render 'header' %>`と書くことが一般的です。
- 省略形の方がシンプルで読みやすいため、推奨されています⭕️

```erb
<!-- シンプルで読みやすい -->
<%= render 'header' %>
<!-- 冗長 -->
<%= render partial: 'header' %>
```
:::

# パーシャルにデータを渡す
パーシャルにデータを渡すには、`render`メソッドの第2引数にローカル変数を指定します！
例えば、以下のようにユーザー情報を表示するパーシャルを作成し、データを渡すことができます。

```erb
<!-- app/views/users/_card.html.erb -->
<div class="card">
  <h3><%= user.name %></h3>
  <p><%= user.email %></p>
  <p>年齢: <%= user.age %>歳</p>
</div>
```

```erb
<!-- app/views/users/show.html.erb -->
<h1>ユーザー詳細</h1>
<!-- ここでパーシャルを呼び出す -->
<%= render 'users/card', locals: { user: @user } %>
```

上記の例では、`_card.html.erb`というパーシャルに`user`というローカル変数を渡しています。パーシャル内では`@user`ではなく、`user`変数を使用してユーザー情報を表示しています。

:::note info
**省略形でのデータ渡し**
- `locals:`オプションも省略可能であり、単に`<%= render 'users/card', user: @user %>`と書くことができます。
- こちらの方がシンプルで読みやすいため、推奨されています⭕
```erb
<!-- シンプルで読みやすい -->
<%= render 'users/card', user: @user %>
```
:::

複数のデータを渡す場合も同様に、カンマ区切りで指定できます。

```erb
<%= render 'users/card', locals: { user: @user, premium: true } %>

<!-- 省略形 -->
<%= render 'users/card', user: @user, premium: true %>
```
パーシャル内では、`user`と`premium`というローカル変数を使用できます。

:::note info
**Railsの規約に沿った省略形**
`app/views/users/`ディレクトリ内で、`_user.html.erb`というパーシャルを作成し、そのパーシャルに変数`user`、インスタンス変数`@user`を渡す場合、Railsの規約で以下のように書くことができます。

```erb
<!-- 通常 -->
<%= render 'user', user: @user %>

<!-- 規約に沿った省略形 -->
<%= render @user %>
```

Railsは、パーシャル名とローカル変数名とインスタンス変数名が一致している場合、自動的に対応付けを行うので、上記のように省略形で書くことができます。

:::

**変数名をカスタマイズ**
パーシャルに渡す変数名をカスタマイズしたい場合は、`as:`オプションを使用します。

```erb
<%= render partial: 'user', collection: @users, as: :account %>

<!-- パーシャル内では account 変数を使用 -->
```

```erb
<!-- app/views/users/_user.html.erb -->
<div class="card">
  <h3><%= account.name %></h3>
  <p><%= account.email %></p>
</div>
```

# コレクションのレンダリング
複数のデータをリスト表示する場合、パーシャルをコレクションとしてレンダリングすることができます！
例えば、全てのユーザー情報を表示する場合、以下のように書くことができます。

```erb
<!-- これまでのパーシャルの説明 -->
<% @users.each do |user| %>
  <%= render partial: 'user', locals: {user: user} %>
<% end %>

<!-- コレクションのレンダリング -->
<%= render partial: 'user', collection: @users %>

```

上記の例では、`@users`コレクションの各要素に対して`_user.html.erb`パーシャルがレンダリングされます。パーシャル内では、`user`というローカル変数が使用できます！

:::note info
**Railsの規約に沿った省略形**
`@users`インスタンス変数がUserインスタンスのコレクションであり、`app/views/users/`ディレクトリ内で、`_user.html.erb`というパーシャルを作成している場合、Railsの規約で以下のように書くことができます。

```erb
<!-- 通常 -->
<%= render partial: 'user', collection: @users %>

<!-- 規約に沿った省略形 -->
<%= render @users %>
```
:::

**繰り返し表示されるパーシャルの間の余白や区切りを調整した場合**
`spacer_template:`オプションを使用して、パーシャルの間に挿入する区切り用のパーシャルを指定できます。

```erb
<%= render partial: 'user', spacer_template: 'user_separator' %>
```

```erb
<!-- app/views/users/_user_separator.html.erb -->
<hr class="user-separator">
```

結果
```html
ユーザー1の情報
<hr class="user-separator">
ユーザー2の情報
<hr class="user-separator">
ユーザー3の情報
```

# まとめ
パーシャルは、再利用可能なビューの部品を切り出して定義するための仕組みであり、コードの重複を避け、保守性を向上させることができます⭕️
`render`メソッドを使用してパーシャルを呼び出し、必要に応じてデータを渡すことができます🧑‍💻
コレクション（配列）に対しても`<%= render @users %>`のように簡潔に記述でき、複数のデータを効率的に一覧表示できます⭐️
パーシャルを活用して、Railsアプリケーションのビューをより効率的に構築しましょう！🚀

# 参考記事
https://railsguides.jp/action_view_overview.html#%E3%83%91%E3%83%BC%E3%82%B7%E3%83%A3%E3%83%AB
