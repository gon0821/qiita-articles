---
title: 【Rails初学者】バリデーションの基礎についてざっくり理解する -Part2（各種バリデータの詳細）
tags:
  - Rails
  - バリデーション
  - 初学者
private: false
updated_at: '2025-12-14T01:10:44+09:00'
id: e4dadfcf07036b1f70c3
organization_url_name: null
slide: false
ignorePublish: false
---
# 続き
https://qiita.com/gon0821/items/2e9d1bccf1ff9e8ab9e1

[前回の記事](https://qiita.com/gon0821/items/2e9d1bccf1ff9e8ab9e1)では、Railsのバリデーションのおおまかな概要についてまとめました📘
今回は具体的なバリデータについてまとめていきたいと思います！

![validation-flow2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/f7e7426a-b41e-4d09-8316-d0d02815f27a.png)

# バリデーションの種類
ここからは実際にコードにどのようにバリデーションを記述するかまとめていきます！

先に今回紹介するバリデーションを一覧しました✍️
| バリデーション名       | 説明                                   |
|----------------------|----------------------|
| `presence`           | 属性が空で**ない**ことを検証               |
| `absence`            | 属性が空で**ある**ことを検証               |
| `uniqueness`         | 属性の値が**ユニーク**であることを検証   |
| `inclusion`          | 属性の値が特定のセットに**含まれている**ことを検証 |
| `exclusion`          | 属性の値が特定のセットに**含まれていない**ことを検証 |
| `acceptance`        | 利用規約などのチェックボックスに**チェックが入っている**ことを検証 |

## バリデーションの定義方法
`ActiveRecord`では自分でバリデーションのルールを書かずとも、すでに多数のバリデーションが用意されているので、まずそれらについて確認していきます！

`ActiveModel`にて提供されている`validates`メソッドを使用して、モデルクラス内でバリデーションを定義します。

```ruby
validates :属性名, バリデーションの種類: オプション
```

## `presence`
指定した属性が「空でない」ことを検証するバリデーションです。最もよく使われるバリデーションの1つです⭐️

```ruby
class Book < ApplicationRecord
  validates :title, presence: true
end
```
この場合`title`が空であればエラーになり、エラーメッセージは「`Title can't be blank`」となります。

関連付けが存在することを確認したい場合、外部キーが存在するのかどうかを検証するのではなく、関連付けられたオブジェクト自体が存在するかどうかを検証します。

```ruby
class Book < ApplicationRecord
  # ❌ 間違い: author_idが存在するか検証
  validates :author_id, presence: true

  # ⭕ 正しい: authorオブジェクトが存在するか検証
  belongs_to :author
  validates :author, presence: true
end
```

また`boolean`型の属性に対して、存在を検証する場合は注意が必要です！
同じように`presence: true`を使用すると、`false`の場合にエラーとなってしまいます😥
`false`も有効な値として扱いため、`inclusion`または`exclusion`を使用して検証する必要があります。

```ruby
Class Book < ApplicationRecord
  # ❌ 間違い: falseの場合にエラーになる
  validates :published, presence: true

  # ⭕ 正しい: trueまたはfalseを許可
  validates :published, inclusion: [true, false]
  # または
  validates :published, exclusion: [nil]
end
```

:::note info
空でないことをどうやってチェックしているの？

`presence`バリデータの内部では、[Object#blank?メソッド](https://api.rubyonrails.org/v8.1/classes/Object.html#method-i-blank-3F)を使用して、属性の値が空であるかどうかを判断しています。
具体的には、以下の条件で空とみなされます。
- `nil`である
- 空の文字列（`""`）
- スペース、タブ、改行の空白のみの文字列（`"   "`）
- 空の配列（`[]`）
- 空のハッシュ（`{}`）

:::

## `absence`
`presence`バリデータの逆の動作をします。指定した属性が「空である」ことを検証します。また検証内容の性質上、`if`オプションと組み合わせた条件付きバリデーションで使用される場面が多いです。

```ruby
class Book < ApplicationRecord
  validates :coupon_code, absence: true, if: :gest_user?
end
```
この場合、`gest_user?`メソッドが`true`を返す場合にのみ、`coupon_code`が空であることを検証します。エラーメッセージは「`Coupon code must be blank`」となります。

空であることのチェックは、[`Object#present?`](https://api.rubyonrails.org/v8.1/classes/Object.html#method-i-present-3F)メソッドを使用して行われます！

## `uniqueness`
属性の値が一意（ユニーク）であり他のレコードと重複していないことを検証します。メールアドレスやユーザー名など、重複を許さない属性に対してよく使用されます。

```ruby
class User < ApplicationRecord
  validates :email, uniqueness: true
end
```
この場合、`email`が他のレコードと重複している場合にエラーとなり、エラーメッセージは「`Email has already been taken`」となります。

:::note warn
データベースレベルの**ユニーク制約**も必要

- `uniqueness`バリデーションはアプリケーションレベルでの検証であり、同時に複数のリクエストが来た場合などに重複レコードが作成される可能性があります😱
- この現象を[レースコンディション](https://www.ipa.go.jp/archive/security/vuln/programming/cc/chapter4/cc4-4.html)と言うらしいです💦
- そのため、データベースレベルでもユニーク制約を設定することがベストプラクティスとされています！
:::

#### `scope`オプション
特定のカラムの組み合わせで一意性を検証したい場合は、`scope`オプションを使用します。

```ruby
class Employee < ApplicationRecord
  validates :email, uniqueness: { scope: :organization_id }
end
```
この場合、同じ`organization_id`（組織）内で`email`が一意であることを検証します。

複数カラムの組み合わせで一意性を検証したい場合は、`scope`オプションに配列を渡します。

```ruby
class Employee < ApplicationRecord
  validates :email, uniqueness: { scope: [:organization_id, :department_id] }
end
```
この場合、同じ`organization_id`（組織）かつ`department_id`（部署）内で`email`が一意であることを検証します。

#### `case_sensitive`オプション
デフォルトでは`uniqueness`バリデーションは大文字と小文字を区別しますが、`case_sensitive: false`オプションを指定することで、大文字と小文字を区別せずに検証することができます。

```ruby
class User < ApplicationRecord
  validates :username, uniqueness: { case_sensitive: false }
end
```

#### `conditions`オプション
特定の条件に基づいて一意性を検証したい場合は、`conditions`オプションを使用します。
SQLの`WHERE`句に相当する条件を指定できます。

```ruby
class Article < ApplicationRecord
  validates :title, uniqueness: { conditions: -> { where(status: 'published') }
  }
end
```
この場合、`status`が`published`のレコードに対してのみ`title`の一意性を検証します。

## `inclusion`と`exclusion`
> これらのバリデータは、属性の値が特定のセットに「含まれているか」「含まれていないか」をチェックします。
指定するセットには、任意の`enumerable`オブジェクト（配列や`range`、`proc`や`lambda`やシンボルで動的に生成されたコレクションなど）を利用できます。
<sub>参考：[Railsガイド バリデーションの使い方](https://railsguides.jp/active_record_validations.html#inclusion%E3%81%A8exclusion)より</sub>

このように、自分でセットを指定して、その中に含まれているか確認したいときは`inclusion`、含まれていないかを検証したいときは`exclusion`を使用します。

```ruby
class User < ApplicationRecord
  validates :age, inclusion: { in: 18..65 }
  validates :username, exclusion: { in: %w[admin root superuser] }
end
```
この場合、`age`が18から65の範囲内であることを検証し、`username`が`admin`、`root`、`superuser`のいずれかでないことを検証します。
`in`オプションで値のセットが渡せれるようになっています。

## `acceptance`
ユーザーがチェックボックスにチェック✅を入れたかどうかを検証するバリデーションです。
利用規約への同意やプライバシーポリシーの確認など、ユーザーの明示的な同意が必要な場面で使用します。

```ruby
class User < ApplicationRecord
  validates :terms_of_service, acceptance: true
end
```
この場合、`terms_of_service`属性が`true`であることを検証します。エラーメッセージは「`Terms of service must be accepted`」となります。

このバリデータの特徴としては、データベースにカラムが存在しなくても動作する点です。
具体的にはフォームから送信された値だけを検証し、データベースには保存されません。

#### `acceptance`オプション
`acceptance`オプションを使用して、受け入れ可能な値をカスタマイズできます。デフォルトでは`[1, true]`が受け入れ可能な値ですが、他の値も指定できます。

```ruby
class User < ApplicationRecord
  validates :terms_of_service, acceptance: { accept: ['yes', 'YES', 'Y'] }
end
```
この場合、`terms_of_service`属性が`'yes'`、`'YES'`、または`'Y'`であることを検証します。


# 参考記事
https://railsguides.jp/active_record_validations.html
