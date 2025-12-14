---
title: 【Rails初学者】バリデーションの基礎についてざっくり理解する -Part4（共通オプション）
tags:
  - ''
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# Part3 続き
https://qiita.com/gon0821/items/f1b92cb70c8adaa8f1d5

[前回の記事](https://qiita.com/gon0821/items/f1b92cb70c8adaa8f1d5)では、Railsの各種バリデータについてまとめました📙
今回はバリデーションで使用できる共通オプションと条件付きバリデーションについてまとめていきたいと思います！

![validation-flow2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/f7e7426a-b41e-4d09-8316-d0d02815f27a.png)

# バリデーションの共通オプション
[Part2](https://qiita.com/gon0821/items/e4dadfcf07036b1f70c3), [Part3](https://qiita.com/gon0821/items/f1b92cb70c8adaa8f1d5)で紹介した各種バリデーションには、共通して使用できるオプションがいくつか存在します！ここではそれらについてまとめていきます✍️

| オプション名         | 説明                                   |
|----------------------|----------------------|
| [`message`](#message)            | カスタムエラーメッセージを指定               |
| [`on`](#on)                 | バリデーションを実行するタイミングを指定       |
| [`allow_nil`](#allow_nil)        | 属性の値が`nil`の場合、バリデーションをスキップ   |
| [`allow_blank`](#allow_blank)      | 属性の値が空文字列または`nil`の場合、バリデーションをスキップ |
| [`if`](#ifとunless)                 | 条件が真の場合にのみバリデーションを実行       |
| [`unless`](#ifとunless)           | 条件が偽の場合にのみバリデーションを実行       |

## `message`
デフォルトのエラーメッセージをカスタムメッセージに変更したい場合に使用します！
エラーメッセージは文字列または`Proc`で指定できます。このオプションを使用しない場合は、各バリデーションに応じたデフォルトのエラーメッセージが使用されます。

```ruby
class User < ApplicationRecord
  validates :name, presence: { message: "ユーザー名を省略することはできません" }
  validates :age, numericality: { message: "%{value}は有効な数値ではありません" }
end
```

文字列の場合、`%{value}`、`%{attribute}`、`%{model}`のいずれか、またはそれらの組み合わせを使用して、属性の値、属性名、モデル名をメッセージに含めることができます！
これらのプレースホルダは、バリデーションが失敗したときに対応する値に置き換えられます。

>`Proc`の`:message`値は、次の2つの引数を受け取ります。
>- バリデーションの対象となるオブジェクト
>- :modelと:attributeと:valueのキーバリューペアを含むハッシュ
<sub>参考：[Railsガイド バリデーションの使い方](https://railsguides.jp/active_record_validations.html#message)より</sub>

```ruby
class User < ApplicationRecord
  validates :email,
    uniqueness: {
      message: -> (object, data) do
        "#{object.name}様、#{data[:value]}はすでに使用されています。別のメールアドレスを入力してください。"
      end
    }
end
```

`Proc`を使用することで、動的にエラーメッセージを生成することができます！

## `on`
バリデーションを実行するタイミングを指定します！
デフォルトでは、すべての保存操作（`create`および`update`）でバリデーションが実行されますが、`on`オプションを使用して特定のタイミングでのみバリデーションを実行するように制限できます。

```ruby
class User < ApplicationRecord
  # 値が重複していてもnameを更新できる
  validates :name, presence: true, on: :create
  # 値が重複していてもemailを作成できる
  validates :email, presence: true, on: :update

  # デフォルトでは、作成・更新の両方でバリデーションが実行される
  validates :age, numericality: { only_integer: true }
end
```

- `:create` - レコードが新規作成されるときにのみバリデーションを実行します。
- `:update` - 既存のレコードが更新されるときにのみバリデーションを実行します。

## `allow_nil`
属性の値が`nil`の場合、バリデーションをスキップします！

```ruby
class User < ApplicationRecord
  validates :password, format: { with: /\A[a-zA-Z0-9]+\z/ }, allow_nil: true
end
```

この場合、`password`属性が`nil`の場合、`format`バリデーションはスキップされます。

## `allow_blank`
属性の値が空文字列または`nil`の場合、バリデーションをスキップします！
使用場面は、入力フォームの任意項目などで空文字列が許容されるときが多いです。

```ruby
class User < ApplicationRecord
  validates :nickname, length: { maximum: 30 }, allow_blank: true
end
```
この場合、`nickname`属性が空文字列または`nil`の場合、`length`バリデーションはスキップされます。

## `if`と`unless`
条件付きでバリデーションを実行したい場合に使用します！
`if`オプションは条件が**真の場合に**のみバリデーションを実行し、`unless`オプションは条件が**偽の場合に**のみバリデーションを実行します。

引数には、シンボル、`Proc`、`Array`が指定できます。

**シンボルを渡す場合**
```ruby
class User < ApplicationRecord
  validates :payment_method, presence: true, if: :premium_member?

  def premium_member?
    membership_type == 'premium'
  end
end
```

**`Proc`を渡す場合**
```ruby
class User < ApplicationRecord
  validates :discount_code, presence: true,
    if: Proc.new { |user| user.age >= 65 }
end
```

`Proc`オブジェクトを使えば、別のメソッドを定義せずに条件を直接指定できます！
シンプルな条件の場合はこちらの方が便利かもしれません。

また`lambda`記法（`->`）も使用できます。
```ruby
class User < ApplicationRecord
  validates :discount_code, presence: true, if: ->(user) { user.age >= 65 }
end
```

# Part5へ続く
今回はバリデーションで使用できる共通オプションについてまとめました📚
次回はカスタムバリデーションについてまとめていきたいと思います！

# 参考記事
https://railsguides.jp/active_record_validations.html
