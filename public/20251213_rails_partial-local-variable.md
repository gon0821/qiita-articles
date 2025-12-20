---
title: "【Rails初学者】パーシャルにはインスタンス変数ではなくローカル変数を渡せ\U0001FAE1"
tags:
  - Rails
  - 初学者
  - パーシャル
private: false
updated_at: '2025-12-21T00:46:09+09:00'
id: 2d912139db7d4a16c175
organization_url_name: null
slide: false
ignorePublish: false
---
# 結論
**パーシャルに変数を渡すときはインスタンス変数（`@users`など）ではなく、ローカル変数（`users`など）を使え**

Bad😱
```erb
<!-- app/views/users/show.html.erb（呼び出し元） -->
<%= render 'card' %>

<!-- app/views/users/_card.html.erb（パーシャル、呼び出し先） -->
<div class="card">
  <h3><%= @user.name %></h3>
  <p><%= @user.email %></p>
</div>
```

Good☺️
```erb
<!-- app/views/users/show.html.erb（呼び出し元） -->
<%= render 'card', locals: { user: @user } %>

<!-- app/views/users/_card.html.erb（パーシャル、呼び出し先） -->
<div class="card">
  <h3><%= user.name %></h3>
  <p><%= user.email %></p>
</div>
```

# はじめに
すいません、タイトルに強い言葉を使ってしまいました。
しかし、パーシャルにインスタンス変数を使うと後々困ることが多いので、この記事でその理由を説明したいと思います🙋

![bleach_aizen_01.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/827f0893-d897-4a8b-90e6-2e4c014906b5.jpeg)
<sub>参考：[BLEACH 20巻](https://www.shueisha.co.jp/books/items/contents.html?isbn=4-08-873883-7)より</sub>

# 図解で理解する
図解でイメージを掴みたいと思います！

**パーシャルにインスタンス変数が渡されたとき😟**

![partial-before.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/11923ab6-2c3c-4763-9c2b-8e418ff36e3e.png)

- `@user`インスタンス変数しか使えず、`author`や`admin`など他のユーザー情報を表示したい場合に対応できず、せっかくパーシャルに切り出したのに再利用性が低いですね😭

**パーシャルにローカル変数が渡されたとき😊**

![partial-after.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/d3be90df-eeb5-410c-bd2c-e6e4d4960b61.png)

- `user`ローカル変数を使うことで、`@user`や`@admin`、`@author`など様々なユーザー情報を渡せて、パーシャルの再利用性が高まります🙌

# なぜローカル変数を使うべきなのか？
## 1. 再利用性が高まる
ローカル変数を使うことで、パーシャルは特定のインスタンス変数に依存しなくなります。
これにより、同じパーシャルを異なるコンテキストで再利用でき、コードの重複を避けることができます！
パーシャルの目的が「共通部分の切り出し」であるため、インスタンス変数を渡してしまうと、その目的が損なわれてしまいます😢

## 2. 依存関係が明確になる
ローカル変数を使うことで、パーシャルがどのデータに依存しているかが明確になります！
例えば、`user`というローカル変数を渡すことで、そのパーシャルがユーザー情報に依存していることが一目で分かります。
一方、インスタンス変数を使うと、パーシャルの中身を見ないと依存関係が分からず、コードの可読性が低下します😵

```erb
<!-- インスタンス変数を使うケース -->
<%= render '_card' %>

<!-- ローカル変数を使うケース -->
<%= render '_card', locals: { user: @user } %>
```

コードを読むうえでこのパーシャルは`user`が必要だと一目でわかり、可読性もあがります📈


## 3. キャッシュが正しく動作する
インスタンス変数や`current_user`などをパーシャル内で直接使うと、データの依存関係が曖昧でどこからデータが来ているか分からなくなり、古いデータが表示されるなどのバグの原因になります。
「必要なデータはすべて渡す」 というルールを守ることで、これらを未然に防ぎ、キャッシュがより確実に効くようになります。

正直ちょっとこの部分は理解しきれていないのですが、[参考記事](https://techracho.bpsinc.jp/hachi8833/2023_03_20/128403)によると、ローカル変数を使うことでキャッシュの依存関係が明確になり、キャッシュの整合性が保たれるとのことです🧐


<sub>参考：[Rails: ビューのパーシャルではローカル変数だけを使うこと（翻訳）](https://techracho.bpsinc.jp/hachi8833/2023_03_20/128403)より</sub>

# 参考記事
https://techracho.bpsinc.jp/hachi8833/2023_03_20/128403

