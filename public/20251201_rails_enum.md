---
title: 【Rails初学者】enumの基礎についてざっくり理解する
tags:
  - Rails
private: false
updated_at: '2025-12-07T00:11:06+09:00'
id: fa78bf309d75852e7fef
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
`enum`についてこれまで雰囲気で使っていたけど、じっくり時間をかけて理解できていないなと感じていたので少し時間をかけて深掘り理解していきたいと思います💪
同じような方や初学者の方の参考になれば嬉しいです！

![enum_bannar.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/33ebb188-5fb6-4c31-9a60-9da1be472c9f.png)

# enumとは
## enumの概要
### enumとはなにか？
`enum`（enumeration: 列挙）は、**名前を整数の定数に割り当てるデータ型**です。
Rails では **Active Record が提供する `enum` 機能**を使うことで、カラムの値を効率的に管理できます！

これだけ聞いてもピンとこないと思うので、この後の具体例などで理解していただけると嬉しいです😊

### enumがあるとなにかメリットあるの？
あります！下記の4点が主なメリットだと思いました！

:::note info
- **読みやすさの向上**
  - 整数の代わりに意味のある名前で状態を扱えるため、プログラムが読みやすくなる
- **メンテナンス性の向上**
  - 状態を名前で管理することで、コードの意図が明確になり、保守しやすくなる
- **高速な処理**
  - 内部的には整数で保存されるため、文字を保存していたときよりデータベース処理が高速になる
- **開発時間の短縮**
  - `enum` が自動生成するメソッドにより、開発時間を大幅に短縮できる

:::

## 基本的な使い方
### enumの定義方法
**STEP1**：enum用の整数型カラムを用意する
**STEP2**：モデルにenumを定義する

これだけです！あとは具体例で見ていきましょう！👀

#### STEP1：enum用の整数型カラムを用意する
具体例ではQiitaの記事のように、記事の状態を管理する`status`カラム（default値：0）を記事のテーブルに用意するとします。

| ID | content | status |
| :--- | :--- | :--- |
| 1 | Modelについて | 0 |
| 2 | Controllerについて | 0 |
| 3 | Viewについて | 0 |

#### STEP2：モデルにenumを定義する
Articleモデルに下記のようにenumを定義します。

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, unpublished: 2 }
end
```

### enumの利用例
enumを利用しない場合、各記事の状態を管理するためにステータスを文字のまま管理しようとすると、DBは下記のようになりますが

| ID | content | status |
| :--- | :--- | :--- |
| 1 | Modelについて | draft |
| 2 | Controllerについて | published |
| 3 | Viewについて | unpublished |

enumを利用することで、下記のように整数で管理できるようになります。

| ID | content | status |
| :--- | :--- | :--- |
| 1 | Modelについて | 0 |
| 2 | Controllerについて | 1 |
| 3 | Viewについて | 2 |

### enumの動作
まず以下のようにカラム名を複数形にすることですべての値を取得、また特定のキーを指定することで`enum`値を取得できます！
```ruby
Article.statuses
=> {"draft" => 0, "published" => 1, "unpublished" => 2}

Article.statuses["draft"]
=> 0

Article.statuses["published"]
=> 1

Article.statuses["deleted"]
=> nil
```

次に、**各ステータスの記事を作成する**としましょう🧑‍💻
```ruby
article = Article.create(content: "Active Recordについて")
article.status
=> "draft"

article = Article.create(content: "Railsのルーティングについて", status: :published)
article.status
=> "published"
```

`status`を指定せず記事を作成した場合、デフォルト値を0にしているため、`draft`として保存されます。
また、`status`カラムには整数値が保存されますが、キーの値として`:published`のようにシンボルを渡すことも可能になっています。
コード上で数字を指定してしまうと可読性が下がってしまうので、シンボルで指定するのが他の人が読んでも分かりやすく良いですね！

:::note info
Q. なんでシンボルで指定できるの？

A. Railsはこの`status`カラムがenumであることを認識して、内部でシンボルを対応する整数値に変換して保存しているからです。
:::

#### 記事のステータスをチェックする
ここでは、記事のステータスが公開済みであれば記事を返す処理の例を見てみましょう！
```ruby
# enumを使わない場合
if article.status == "published"
  article.content
end

# enumを使った場合
if article.published?
  article.content
end
```
このように`enum`を使うことで`boolean`を返すメソッド（ここでは`draft?`,`published?`,`unpublished?`）が自動生成されるため、コードがシンプルで読みやすくなります！

#### 記事のステータスを更新する
記事のステータスを更新したい場合もあるでしょう🤔
このときはどうなるんでしょうか？

```ruby
# enumを使わない場合
article.update(status: "unpublished")

# enumを使った場合
article.unpublished!
```
`enum`を使うことで、ステータスを更新するためのメソッド（ここでは`draft!`,`published!`,`unpublished!`）が自動生成されるため、更新の際もコードがシンプルになっています！

#### 各ステータスの記事を取得する
おそらくステータスを持つレコードを扱うのであれば、各ステータスごとに取得したいときがあるでしょう。
Qiitaにもあるように、`下書き`、`公開`、`非公開`の記事をそれぞれ取得して表示させたいときどうやって取得するんでしょうか？

```ruby
# enumを使わない場合
draft_articles = Article.where(status: "draft")
published_articles = Article.where(status: "published")
unpublished_articles = Article.where(status: "unpublished")

# enumを使った場合
draft_articles = Article.draft
published_articles = Article.published
unpublished_articles = Article.unpublished
```

`enum`を使うことで、各ステータスごとにスコープメソッド（ここでは`draft`、`published`、`unpublished`）が自動生成されるため、このコードが直感的に理解しやすくなっています！

## 応用編
#### そのステータスではない記事を取得する（否定条件）
例えば、`下書き`以外の記事を全て取得したい場合、どうすれば良いでしょうか？

```ruby
# enumを使わない場合
no_draft_articles = Article.where.not(status: "draft")

# enumを使った場合
no_draft_articles = Article.not_draft
```
このように`not_`を付けることで、否定条件のスコープメソッドが自動生成されます！

#### スコープを自動生成しないようにする
場合によっては、スコープメソッドを自動生成したくない場合もあるかもしれません。
（自分でスコープ名をカスタムしたいときや、そのプロジェクトでスコープそのものが不要なときなど）
その場合は、下記のように`scopes: false`オプションを付与することで、自動生成を防ぐことができます。

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, unpublished: 2 }, scopes: false
end
```

#### enumにプレフィックス・サフィックスを付与する
仮にですが、Qiita記事に有料のものが追加されたとします。（できたら無料のまま進めてほしいな〜😌）
その場合、無料か有料かの状態を管理するために`category`カラムを追加するとしましょう。
コードは以下のようになるかと思います。

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, unpublished: 2 }
  enum :category, { free: 0, paid: 1 }
end
```

このとき、`Article.free`や`article.published?`、`article.paid!`のように利用すると、どちらのカラムの`enum`メソッドなのか分かりづらくなってしまう課題があります🤔

このような場合は、`prefix: true`オプションを付与することで、`enum`メソッドにプレフィックスを付与することができ、よりコードが明確になります！

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, unpublished: 2 }, prefix: true
  enum :category, { free: 0, paid: 1 }, prefix: true
end
```
これにより、以下のように`enum`メソッドを利用できるようになります。

```ruby
Article.category_free
article.status_published?
article.category_paid!
```

サフィックスを付与したい場合は、`suffix: true`オプションを付与します。

```ruby
class Article < ApplicationRecord
  enum :status, { draft: 0, published: 1, unpublished: 2 }, suffix: true
  enum :category, { free: 0, paid: 1 }, suffix: true
end
```

これにより、以下のように`enum`メソッドを利用できるようになります。

```ruby
Article.free_category
article.published_status?
article.paid_category!
```

## まとめ
#### 自動生成されるメソッド一覧
まとめると、以下のようなメソッドが自動生成されます。
| メソッド | 説明 |
| :--- | :--- |
| `Model.status_names` | ハッシュでenumの値を取得 |
| `status_name?` | 指定したステータスかどうかを判定 |
| `status_name!` | 指定したステータスに更新 |
| `Model.status_name` | 指定したステータスのレコードを全て取得 |
| `Model.not_status_name` | 指定したステータス以外のレコードを取得 |

# 終わりに
最後まで読んでいただきありがとうございました☺️
Railsは便利な機能がたくさんあるので、これからも少しずつ深掘りしていきたいと思います💪
明日は`ActiveRecord::Relation`について深掘りしていきますので、よければそちらもご覧ください！


# 参考記事
https://railsguides.jp/active_record_querying.html#enum

https://techracho.bpsinc.jp/hachi8833/2022_02_18/115735

https://api.rubyonrails.org/v8.1/classes/ActiveRecord/Enum.html
