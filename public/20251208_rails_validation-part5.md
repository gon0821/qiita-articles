---
title: 【Rails初学者】バリデーションの基礎についてざっくり理解する -Part5（カスタムバリデーション）
tags:
  - Rails
  - バリデーション
  - 初学者
private: false
updated_at: '2025-12-16T04:33:15+09:00'
id: d1197c5009586c7289f4
organization_url_name: null
slide: false
ignorePublish: false
---
# Part4 続き
https://qiita.com/gon0821/items/b35aaf68a978a52db41c

[前回の記事](https://qiita.com/gon0821/items/b35aaf68a978a52db41c)では、バリデーションで使用できる共通オプションについてまとめました📙
今回はカスタムバリデーションについてまとめていきたいと思います！✍️

![validation-flow3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/09a38e8d-f444-40b7-9864-3657a69bf84d.png)

# カスタムバリデーション
Railsには様々な組み込みバリデーションが用意されていますが、それだけでは対応できない場合もあります。そんなときに役立つのがカスタムバリデーションです！
カスタムバリデーションを使用すると、独自のバリデーションロジックを定義して、モデルの属性に対して特定のルールを適用することができます。以下では、カスタムバリデーションの作成方法について説明します。

## カスタムバリデータ
#### `ActiveModel::Validator`を使うケース
カスタムバリデータを作成するには、`ActiveModel::Validator`を継承したクラスを定義し、その中で`validate`メソッドを実装します！
`validate`メソッド内で、バリデーションロジックを記述し、条件に合わない場合は`record.errors.add`メソッドを使用してエラーメッセージを追加します。
カスタムバリデータをモデルに適用するには、`validates_with`メソッドを使用します。

```ruby
class CategoryValidator < ActiveModel::Validator
  def validate(record)
    unless ['Ruby', 'JavaScript', 'TypeScript'].include?(record.category)
      record.errors.add(:category, "is not a valid category")
    end
  end
end

class Article < ApplicationRecord
  validates_with CategoryValidator
end
```

上記の例では、`CategoryValidator`クラスがカスタムバリデータとして定義されており、`Article`モデルに適用されています。
`category`属性が指定されたリストに含まれていない場合、エラーメッセージが追加されます。


#### `ActiveModel::EachValidator`を使うケース
個別の属性に対してカスタムバリデータを追加するには、`ActiveModel::EachValidator`を継承したクラスを定義し、その中で`validate_each`メソッドを実装すると、モデル内で`validates`メソッドを使用して適用できます！

```ruby
class PhoneNumberValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    if value.present? && !value.match?(/\A\d{10,11}\z/)
      record.errors.add(attribute, options[:message] || "is not a valid phone number")
    end
  end
end

class User < ApplicationRecord
  validates :phone_number, phone_number: true
end
```
上記の例では、`PhoneNumberValidator`クラスがカスタムバリデータとして定義されており、`User`モデルの`phone_number`属性に適用されています。
`phone_number`属性が10桁または11桁の数字でない場合、エラーメッセージが追加されます。
モデルでは、`validates`メソッドを使用して、`属性名: true`の形式でカスタムバリデータを指定します。

`validates_each`メソッドは3つの引数を受け取るように実装する必要があり、引数は下記の通りです。
- `record`: バリデーション対象のオブジェクト
- `attribute`: バリデーション対象の属性
- `value`: バリデーション対象の属性の値

:::note info
**`Validator`と`EachValidator`の使い分け**
判断基準としては以下の通りです！

Q.そのルールは、複数のカラムを比較する必要がありますか？
- Yes→`Vaidator`を使用する（例：パスワードとパスワード確認、開始日と終了日）
- No→次の質問へ

Q.そのルールは、個別のカラムだけ見れば判断できますか？
- Yes:`EachValidator`を使用する（例：電話番号、郵便番号、メールアドレス）

`EachValidator`のメリットは、モデル側のコードの可読性が向上することです！
個別の属性に対してカスタムのバリデータを作成する際には、`EachValidator`を使用することを検討してみてください😌
:::

## カスタムメソッド
カスタムメソッドを使用してバリデーションを定義することもできます！
モデル内で`validate`メソッドを使用して、独自のバリデーションメソッドをシンボルで指定します。

```ruby
class User < ApplicationRecord
  validate :phone_number_format

  private
  def phone_number_format
    if phone_number.present? && !phone_number.match?(/\A\d{10,11}\z/)
      errors.add(:phone_number, "is not a valid phone number")
    end
  end
end
```

:::note info
Q. カスタムメソッドか、カスタムバリデータ（`EachValidator`）のどちらを使用すべきか？

**カスタムバリデータ（`EachValidator`）が推奨です！**
理由としては、
- 再利用ができる
- コードの可読性が向上する

「**再利用ができる**」ですが、例えば「電話番号」という概念は、`User`モデル以外にも登場する可能性が非常に高いです。 例えば、将来的に`Company`（会社）や`Store`（店舗）モデルを作った時、下のコードなら一度定義したものを使い回すだけで済みます。
```ruby
class Company < ApplicationRecord
  validates :phone_number, phone_number: true
end

class Store < ApplicationRecord
  validates :phone_number, phone_number: true
end
```
:::

## カスタムコンテキスト
バリデーションの実行タイミングをカスタムコンテキストで制御することも可能です！
`on`オプションでカスタムの名前を指定できます。

カスタムコンテキストの利用場面としては、ウィザード形式の複数ステップがあるフォームがあります。
ウィザード形式のフォームとは、Amazonや楽天の「購入手続き」ページでよく見かける、複数のステップに分かれたフォームのことです。

```ruby
class User < ApplicationRecord
  validate :personal_information, on: :personal_info
  validate :contact_information, on: :contact_info
  validate :location_information, on: :location_info

  private
    def personal_information
      errors.add(:base, "名前は省略できません") if first_name.blank?
      errors.add(:base, "年齢は18歳以上でなければなりません") if age && age < 18
    end

    def contact_information
      errors.add(:base, "メールアドレスは省略できません") if email.blank?
      errors.add(:base, "電話番号は省略できません") if phone.blank?
    end

    def location_information
      errors.add(:base, "住所は省略できません") if address.blank?
      errors.add(:base, "市区町村名は省略できません") if city.blank?
    end
end
```
<sub>参考：[Railsガイド カスタムコンテキスト](https://railsguides.jp/active_record_validations.html#%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%A0%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88)より</sub>

# Part6へ続く
ようやくカスタムバリデーションまでたどり着きました...！🎉
次回は、`errors`オブジェクトの使い方についてまとめていきたいと思います！✍️

https://qiita.com/gon0821/items/09e1194f227e2ea5d506

# 参考記事
https://railsguides.jp/active_record_validations.html
