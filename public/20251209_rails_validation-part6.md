---
title: 【Rails初学者】バリデーションの基礎についてざっくり理解する -Part6（errors/エラー対応）
tags:
  - Rails
  - バリデーション
  - 初学者
private: false
updated_at: '2025-12-16T04:30:53+09:00'
id: 09e1194f227e2ea5d506
organization_url_name: null
slide: false
ignorePublish: false
---
# Part5 続き
https://qiita.com/gon0821/items/d1197c5009586c7289f4

[前回の記事](https://qiita.com/gon0821/items/d1197c5009586c7289f4)では、カスタムバリデーションについてまとめました📙
今回はバリデーションエラーを管理する`errors`についてまとめていきたいと思います！✍️

![validation-flow3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/09a38e8d-f444-40b7-9864-3657a69bf84d.png)

# バリデーションエラーに対応
データの保存時や`valid?`メソッドでバリデーションが実行されますが、失敗したらそれで終わりではなく、どの属性がどのような理由で失敗したのかを知る必要があります。Railsでは、バリデーションエラーにアクセスできるように`errors`メソッドが用意されています。
`errors`メソッドは、`ActiveModel::Errors`オブジェクトを返し、バリデーションエラーに関する情報を提供します。以下では、`errors`オブジェクトの主なメソッドとその使い方について説明します。

## 基本的な使い方
よく使用される`errors`オブジェクトのメソッドをいくつか紹介します！
先に今回出てくるものをまとめておきます✍️
| メソッド名                         | 説明                                   |
|----------------------------------|--------------------------------------|
| [`full_messages`](#full_messages)               | すべてのエラーメッセージを配列で返す               |
| [`errors[:attribute]`](#errorsattribute)          | 指定した属性に関連するエラーメッセージの配列を返す     |
| [`errors.where`](#errorswhere)               | 条件に一致するエラーメッセージの配列を返す          |
| [`any?`](#any--empty)                         | エラーが**存在する**かどうかを真偽値で返す               |
| [`empty?`](#any--empty)                       | エラーが**存在しない**かどうかを真偽値で返す             |
| [`size` / `count`](#size--count)              | エラーの数を返す                           |
| [`add`](#add)                          | エラーメッセージを手動で追加する                   |
| [`errors[:base]`](#errorsbase)               | オブジェクト全体の状態に関連するエラーを追加できる                |
| [`clear`](#clear)                      | すべてのエラーメッセージをクリアする                  |

## `full_messages`
`full_messages`メソッドは、オブジェクトのすべてのエラーメッセージを配列で返します！
各メッセージは属性名とエラーメッセージを組み合わせた形式で提供されます。
エラーメッセージをユーザーに表示する際によく使われます。

```ruby
class User < ApplicationRecord
  validates :name, presence: true
  validates :email, format: { with: URI::MailTo::EMAIL_REGEXP }
end

user = User.new(name: "", email: "invalid_email")
user.valid? # => false
user.errors.full_messages
# => ["Name can't be blank", "Email is invalid"]
```

## `errors[:attribute]`
特定の属性に関連するエラーメッセージを取得したい場合は、`errors[:attribute]`の形式でアクセスします！
この方法では、指定した属性に関連するエラーメッセージの配列が返されます。
属性に関連するエラーがない場合は、空の配列が返されます。

```ruby
class Company < ApplicationRecord
  validates :name, presence: true, uniqueness: true
end

company = Company.new(name: "")
company.valid? # => false
company.errors[:name]
# => ["Name can't be blank"]
```

## `errors.where`
各エラーは`ActiveModel::Error`オブジェクトとして表現されており、`errors.where`メソッドを使用して条件に一致するエラーメッセージの配列を取得できます。

```ruby
class Product < ApplicationRecord
  validates :name, presence: true, length: { minimum: 3 }
end

product = Product.new
product.valid? # => false
product.errors.where(:name)
# => [#<ActiveModel::Error attribute=:name, type=:blank>, #<ActiveModel::Error attribute=:name, type=:too_short, options={:count=>3}>]
```

このように`errors.where(:attr)`の第1引数に属性名を指定すると、その属性に関連するエラーオブジェクトの配列が返されます。
第2引数に`type`オプションを指定することで、特定の種類のエラーをフィルタリングすることも可能です。

```ruby
product.errors.where(:name, type: :blank)
# => [#<ActiveModel::Error attribute=name, type=blank, options={}>]
```

これらによって取得した`error`オブジェクトから情報を取り出すことができます。

```ruby
error = product.errors.where(:name).last
error.message
# => "is too short (minimum is 3 characters)"
error.full_message
# => "Name is too short (minimum is 3 characters)"
error.attribute
# => :name
error.type
# => :too_short
error.options[:count]
# => 3
```

## `any?` / `empty?`
`any?`メソッドは、オブジェクトにエラーが**存在する**かどうかを真偽値で返します。一方、`empty?`メソッドは、エラーが**存在しない**かどうかを真偽値で返します！

```ruby
class Order < ApplicationRecord
  validates :total_amount, numericality: { greater_than: 0 }
end

order = Order.new(total_amount: -10)
order.valid? # => false
order.errors.any?
# => true

order.errors.empty?
# => false
```

`any?`のよくある使い方として、Viewでエラーがあれば`full_messages`を表示するときの判定で使用されています。

## `size` / `count`
`size`メソッドまたは`count`メソッドを使用して、オブジェクトに存在するエラーの数を取得できます！

```ruby
class Customer < ApplicationRecord
  validates :name, presence: true
  validates :email, format: { with: URI::MailTo::EMAIL_REGEXP }
end

customer = Customer.new(name: "", email: "invalid_email")
customer.valid? # => false
customer.errors.size
# => 2

customer.errors.count
# => 2
```

エラーメッセージにて「`〇〇件のエラーがあります`」といった表現をする際に、`size`や`count`メソッドが役立ちます。

## `add`
`add`メソッドを使用して、手動でエラーメッセージを追加することができます！
具体的には「属性名」「エラー種別」「オプションの追加ハッシュ」を引数として受け取り、`error`オブジェクトを作成します。
このメソッドは、カスタムバリデーションメソッド内で細かくエラー内容を設定したいときによく使用されます。

```ruby
class School < ApplicationRecord
  validate do |school|
    if school.students.count < 10
      school.errors.add(:students, :too_few, message: "生徒数が少なすぎます。最低10人必要です。")
    end
  end
end
```

## `errors[:base]`
`errors[:base]`を使用すると、オブジェクト全体の状態に関連するエラーを追加できます！
「どの属性の問題か特定できない、またはモデル全体に関わる問題」に対してエラーメッセージを設定する際に使われます。

```ruby
class Booking < ApplicationRecord
  validate :validate_booking_rules

  private

  def validate_booking_rules
    # 特定の曜日は予約不可
    if booking_date.wday == 0  # 日曜日
      errors.add(:base, "日曜日は予約できません")
    end

    # 同じ時間帯に既に予約がある
    if conflicting_bookings_exist?
      errors.add(:base, "選択した時間は既に予約されています")
    end

    # 予約期限を過ぎている
    if booking_date < 3.days.from_now
      errors.add(:base, "予約は3日前までに行ってください")
    end
  end
end

booking = Booking.new(booking_date: Date.today)
booking.valid? # => false
booking.errors[:base]
# => ["日曜日は予約できません", "選択した時間は既に予約されています", "予約は3日前までに行ってください"]
```

:::note info
**`:base`を使うべき場面**

- 複数の属性にまたがる検証
- モデル全体のビジネスルール
- 外部API連携のエラー
- どの属性にも紐づけられないエラー

**メリット**

- ユーザーに全体的な問題を伝えられる
- 特定のフィールドに紐づけない柔軟性
- 複雑なビジネスロジックに対応
:::

## `clear`
`clear`メソッドを使用して、オブジェクトのすべてのエラーメッセージをクリア（削除）することができます！
ただ、`valid?`メソッドや保存メソッドを呼び出すと自動的にクリアされるため、手動でクリアする必要はあまりありません。

```ruby
class User < ApplicationRecord
  validates :name, presence: true
end

user = User.new(name: "")
user.valid?  # => false
user.errors.clear
user.errors.any?
# => false
```

# おわりに
長かったRailsバリデーションシリーズも今回で終了です！🎉
バリデーションについて全体像が掴めたと思いますので、実際のプロダクトでも意図していないデータが保存されないように積極的に活用したいと思います！
最後まで見ていただき、ありがとうございました😊

# 参考資料
https://railsguides.jp/active_record_validations.html

# これまでのバリデーションに関する記事
https://qiita.com/gon0821/items/2e9d1bccf1ff9e8ab9e1

https://qiita.com/gon0821/items/e4dadfcf07036b1f70c3

https://qiita.com/gon0821/items/f1b92cb70c8adaa8f1d5

https://qiita.com/gon0821/items/b35aaf68a978a52db41c

https://qiita.com/gon0821/items/d1197c5009586c7289f4


