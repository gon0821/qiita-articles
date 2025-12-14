---
title: 【Rails初学者】バリデーションの基礎についてざっくり理解する -Part3（各種バリデータの詳細）
tags:
  - Rails
  - バリデーション
  - 初学者
private: false
updated_at: '2025-12-14T12:11:43+09:00'
id: f1b92cb70c8adaa8f1d5
organization_url_name: null
slide: false
ignorePublish: false
---
# Part2 続き
https://qiita.com/gon0821/items/e4dadfcf07036b1f70c3

[前回の記事](https://qiita.com/gon0821/items/e4dadfcf07036b1f70c3)では、Railsの各種バリデータについて一部まとめました📙
今回は残りのRailsが標準で用意しているバリデーションについてまとめていきたいと思います！

![validation-flow2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/f7e7426a-b41e-4d09-8316-d0d02815f27a.png)

# バリデーションの種類（続き）
先に今回紹介するバリデーションを一覧にしています✍️
| バリデーション名       | 説明                                   |
|----------------------|----------------------|
| `confirmation`      | 属性の値が**対応する確認属性の値と一致**していることを検証 |
| `comparison`      | 属性の値が他の属性の値と**比較条件を満たしている**ことを検証 |
| `format`            | 属性の値が特定の**正規表現パターンに一致している**ことを検証 |
| `length`            | 属性の値の**長さが特定の条件を満たしている**ことを検証 |
| `numericality`      | 属性の値が**数値である**ことを検証 |

## `confirmation`
指定した属性の値が、対応する確認属性（`属性名_confirmation`）の値と一致していることを検証するバリデーションです！
主にパスワードやメールアドレスの確認入力などで使用されます🔐

```ruby
class User < ApplicationRecord
  validates :password, confirmation: true
end
```
この場合、`password`属性に対して確認属性である`password_confirmation`が自動的に生成され、両者の値が一致しない場合にバリデーションエラーとなります。

ビューテンプレートでは、以下のように確認属性の入力フィールドを追加します。

```erb
<%= form_with model: @user do |form| %>
  <%= form.label :password %>
  <%= form.password_field :password %>
  <%= form.label :password_confirmation %>
  <%= form.password_field :password_confirmation %>
<% end %>
```
:::note warn
**注意点**
- 確認属性はデータベースに保存されないため、マイグレーションファイルには含める必要はありません。
- `confirmation`バリデーションは、対応する確認属性が存在しない場合には無視されます。（`属性名_confirmation`が`nil`でない場合のみ、検証される）
- そのため確認を必須にするには、以下のように`presence`バリデータも併用することが推奨されます。

```ruby
class User < ApplicationRecord
  validates :password, confirmation: true
  validates :password_confirmation, presence: true
end
```
:::

#### `case_sensitive`オプション
`confirmation`バリデーションでは、`case_sensitive`オプションを使用して大文字と小文字の区別を設定できます！
デフォルトでは`true`であり、大文字と小文字を区別しますが、`false`に設定すると区別しなくなります。
```ruby
class User < ApplicationRecord
  validates :password, confirmation: { case_sensitive: false }
end
```

一般的には、このバリデータは`if`オプションと組み合わせて使用され、初期状態からの変更時にのみ確認を要求することが多いです（例えば、他項目の更新時でパスワードの確認項目には入力が必要ないときにもエラーが出てしまうのを防ぐため）

```ruby
class User < ApplicationRecord
  validates :password, confirmation: true
  validates :password_confirmation, presence: true, if: :password_changed?
end
```

## `comparison`
指定した属性の値が、渡された値と比較条件を満たしていることを検証するバリデーションです！

```ruby
class Event < ApplicationRecord
  validates :start_date, comparison: { less_than: :end_date }
end
```
この場合、`start_date`属性の値が`end_date`属性の値よりも小さいことを検証します。

比較条件には以下のオプションが使用できます。
| オプション名        | 説明                 | エラーメッセージ |
|------------------|----------------------|------------------|
| `:equal_to`      | 指定した値と**等しい**ことを検証 | "must be equal to %{count}" |
| `:greater_than`  | 指定した値より**大きい**ことを検証 | "must be greater than %{count}" |
| `:greater_than_or_equal_to` | 指定した値**以上**であることを検証 | "must be greater than or equal to %{count}" |
| `:less_than` | 指定した値より**小さい**ことを検証 | "must be less than %{count}" |
| `:less_than_or_equal_to` | 指定した値**以下**であることを検証 | "must be less than or equal to %{count}" |
| `:other_than` | 指定した値と**等しくない**ことを検証 | "must be other than %{count}" |

:::note info
比較対象には、固定値（数値や日付など）や他の属性名をシンボルで指定することができます。
:::

## `format`
指定した属性の値が、特定の正規表現パターンに一致していることを検証するバリデーションです！
主にメールアドレスや電話番号などの形式を検証する際に使用されます📧

`with`オプションに正規表現を指定して使用します。

```ruby
class Article < ApplicationRecord
  validates :url, format: { with: /\A[a-zA-Z0-9]+\z/ , message: 'は英数字のみ使用できます' }
end
```
この場合、`url`属性の値が英数字のみで構成されていることを検証します。

:::note info
`without`オプションを使用すると、指定した正規表現パターンに**一致しない**ことを検証できます。
:::

:::note warn
**注意点**
- 正規表現は`/\A...\z/`の形式で指定することが推奨されます。これは文字列全体がパターンに一致することを保証するためです。`^`や`$`は、1行の冒頭や末尾にマッチさせる場合に使用するべきであるため、文字列で使用しないようにしましょう。
:::

## `length`
指定した属性の値の長さが、特定の条件を満たしていることを検証するバリデーションです！
文字列や配列の長さを検証する際に使用されます📏

```ruby
class User < ApplicationRecord
  validates :username, length: { minimum: 3, maximum: 20 }
  validates :password, length: { in: 8..16 }
  validates :student_id, length: { is: 10 }
end
```

この場合、下記をを検証します。
- `username`属性の長さが3文字以上20文字以下であること
- `password`属性の長さが8文字から16文字の範囲内であること
- `student_id`属性の長さがちょうど10文字であること

利用できるオプションは以下の通りです。
| オプション名        | 説明                 |
|------------------|----------------------|
| `:minimum`       | 指定した値より**大きいまたは等しい**ことを検証 |
| `:maximum`       | 指定した値より**小さいまたは等しい**ことを検証 |
| `:is`            | 指定した値と**等しい**ことを検証 |
| `:in` or `:within` | 指定した範囲内であることを検証 |

:::note info
オプションは一度に一つしか利用できませんが、`minimum`と`maximum`は同時に指定可能です。
:::

## `numericality`
指定した属性の値が数値であることを検証するバリデーションです！🔢
デフォルトでは整数と浮動小数点数の両方を許可しますが、オプションを使用してさらに細かい制御が可能です。また、正負の符号がある場合もマッチします。

```ruby
class Product < ApplicationRecord
  validates :points, numericality: true
  validates :price, numericality: { only_integer: true }
end
```
この場合、下記を検証します。
- `points`属性の値が数値であること
- `price`属性の値が整数であること

以下のオプションが使用できます。
| オプション名        | 説明                 | エラーメッセージ |
|------------------|----------------------|------------------|
| `:only_integer` | 整数であることを検証 | "must be an integer" |
| `:in` | 指定した範囲内であることを検証 | "must be in %{count}" |
| `odd` | 奇数であることを検証 | "must be odd" |
| `even` | 偶数であることを検証 | "must be even" |
| `:equal_to`      | 指定した値と**等しい**ことを検証 | "must be equal to %{count}" |
| `:greater_than`  | 指定した値より**大きい**ことを検証 | "must be greater than %{count}" |
| `:greater_than_or_equal_to` | 指定した値**以上**であることを検証 | "must be greater than or equal to %{count}" |
| `:less_than` | 指定した値より**小さい**ことを検証 | "must be less than %{count}" |
| `:less_than_or_equal_to` | 指定した値**以下**であることを検証 | "must be less than or equal to %{count}" |
| `:other_than` | 指定した値と**等しくない**ことを検証 | "must be other than %{count}" |

:::note info
`only_integer`を`true`に設定したときに、使用される正規表現
```bash
/\A[+-]?\d+\z/
```
- 先頭に`+`または`-`が0回または1回出現
- 続いて1つ以上の数字（0-9）が続く
- 文字列全体がこのパターンに一致することを検証
:::

:::note warn
**注意点**
- デフォルトでは属性の値が`nil`は許容されないので、`nil`を許可したい場合は、`allow_nil: true`オプションを使用する。
- IntegerカラムやFloatカラムでは、空の文字列が`nil`に変換されるので注意。
:::

# Part4へ続く
Railsが用意してくれているバリデーションについて調べてきましたが、これらを自分で用意するとなるとかなり大変なので、Rails様々だなと改めて感じました😌
次回はバリデーションの共通オプションや条件付きバリデーションについてまとめていきたいと思います💪

# 参考記事

https://railsguides.jp/active_record_validations.html
