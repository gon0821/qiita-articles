---
title: 【Rails初学者】ActiveRecordの基礎についてざっくり理解する
tags:
  - Rails
  - ActiveRecord
  - model
  - 初学者
private: false
updated_at: '2025-12-09T22:18:06+09:00'
id: b5db46f91da450427fa8
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
Railsの`ORM`である`ActiveRecord`について、恩恵を受けていたにもかかわらず根本的な理解の部分が足りていないと感じていたので、改めて基礎から理解を深めていきたいと思います💪
同じような方や初学者の方の参考になれば嬉しいです！

![find,find_by,where.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/e816d13d-9cb5-4d4e-bd90-a750ecc697cf.png)

# ActiveRecordとは
## ActiveRecordの概要
### ORMとはなにか？
先ほどしれっと`ORM`という言葉を使いましたが、そもそもこれはなにか？というところから説明します。
まず、`ORM`とは**Object-Relational Mapping**の略で、**オブジェクト指向プログラミングとリレーショナルデータベースの間の橋渡しをする技術や手法**のことを指します。
簡単に言うと、データベースのテーブル（表形式のデータ）を、プログラミング言語のオブジェクト（クラスやインスタンス）として扱えるようにする仕組みです。

#### 対応関係
| リレーショナルデータベース | オブジェクト指向プログラミング |
|----------------------------|-------------------------------|
| テーブル                   | クラス                        |
| レコード（行）             | オブジェクト（インスタンス）  |
| カラム（列）               | 属性（プロパティ）            |

:::note info
ORMの利点
- **抽象化**：SQL文を書く必要が減り、コードがシンプルになる
- **生産性向上**：データベース操作が簡単になり、開発速度が向上する
- **保守性向上**：コードが読みやすくなり、保守が容易になる

:::

#### 具体例
```ruby
# ORMなし（生SQL）
result = db.execute("SELECT * FROM users WHERE id = 1")
name = result[0]["name"]

# ORMあり（ActiveRecord）
user = User.find(1)
name = user.name
```

ORMがあることによって、すごく直感的に分かるかなと思います😊

### ActiveRecordとはなにか？
今回の主役である`ActiveRecord`は、Railsで使用されるORMライブラリです。
`ActiveRecord`は、データベースのテーブルをRubyのクラスとして表現し、テーブルのレコードをそのクラスのインスタンスとして扱うことができます。

また、ややこしいのですが、`ActiveRecord`は**デザインパターンの一つ**としての意味もあるため文脈でどちらの意味で使われているか注意が必要です。

ここで、`ActiveRecord`の立ち位置を図に表すと以下のようになります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/3a299c7e-919d-4169-9583-911301107a9c.png)
<sub>参考：[【初心者向け】RailsのActive Recordの解説&メソッドまとめ](https://qiita.com/ryokky59/items/a1d0b4e86bacbd7ef6e8)より</sub>


::: note info
ORMフレームワークとしてのActiveRecordが提供する主な機能
1. **データの取得・保存・更新・削除**
   - find, where, create, update, destroyなど
1. **テーブルとモデルの対応**
   - Userクラス → usersテーブルに自動で対応
1. **データの検証（バリデーション）**
   - 保存前にデータが正しいかチェック
1. **テーブル間の関連付け**
   - has_many, belongs_toなどで関連を定義
:::

## ActiveRecordにおけるルール
一般的なORMでは、設定ファイルに大量のコードを書いて、テーブル名やカラム名、主キーなどを細かく指定する必要があります（自分で一から全て設定するの大変そう😱）

> Railsでは、アプリケーションの設定方法がほとんどの場合に同じになるのであれば、その方法をフレームワークのデフォルト設定にすべきという「設定よりも規約（Convention over Configuration）」の考えを採用しています。
<sub>参考：[Railsガイド ActiveRecord基礎](https://railsguides.jp/active_record_basics.html#active-record%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8Bcoc%EF%BC%88convention-over-configuration%EF%BC%89)より</sub>

このため、Railsの`ActiveRecord`では**規約に従う限り、ほとんど設定不要で利用可能**です。
といっても、最低限知っておくべきルールがいくつかあるので紹介します🧑‍💻

### 命名規約
| モデル名（クラス名） | テーブル名         |
|----------------------|--------------------|
| User                 | users              |
| ProductCategory      | product_categories |
| Person            | people        |

> Railsでは、データベースのテーブル名を探索するときに、モデルのクラス名を複数形にした名前で探索します。たとえば、Bookというモデルクラスがある場合、これに対応するデータベースのテーブルは複数形の「books」になります。
<sub>参考：[Railsガイド ActiveRecord基礎](https://railsguides.jp/active_record_basics.html#%E5%91%BD%E5%90%8D%E8%A6%8F%E7%B4%84)より</sub>

また、モデルのクラス名が`product category`のような複数の単語で構成される場合は、**キャメルケース（CamelCase）**で命名し、対応するテーブル名は**スネークケース（snake_case）**で複数形にします。

### スキーマの規約
1. 主キーは`id`カラムとして自動的に追加され、整数型カラムを利用される
1. タイムスタンプ用の`created_at`および`updated_at`カラムが自動的に追加され、レコードの作成日時と更新日時を管理する
2. 外部キーは`単数系のテーブル名_id`の形式で命名される（例：`user_id`）
3. `lock_version`カラムは楽観的ロックのために使用される
4. `関連付け名_type`カラムではポリモーフィック関連付けを管理する

主な規約は以上ですが、この他にも規約がありますので詳細は[こちら](https://railsguides.jp/active_record_basics.html#%E3%82%B9%E3%82%AD%E3%83%BC%E3%83%9E%E3%81%AE%E8%A6%8F%E7%B4%84)をご参照ください。

## ActiveRecordのモデルを作成する
Railsアプリケーションを作成すると、`app/models/application_record.rb`というファイルが自動生成されます（`Rails8`で動作確認）

```ruby
# app/models/application_record.rb
class ApplicationRecord < ActiveRecord::Base
  primary_abstract_class
end
```

この`ApplicationRecord`クラスは、`ActiveRecord::Base`を継承しており、アプリケーション内のすべてのモデルクラスの基底クラスとして機能します。
各モデルクラスは、この`ApplicationRecord`クラスを継承することで、ActiveRecordの機能を利用できます。
```ruby
class User < ApplicationRecord
end
```

:::note info
なぜ各モデルクラスで`ActiveRecord::Base`を直接継承しないのか？🤔
- 実際、`Rails4`までは各モデルクラスで`ActiveRecord::Base`を直接継承していました。
しかし、`Rails5`からは`ApplicationRecord`クラスを導入し、`ApplicationRecord`を継承する形に変更されました。
- これによりアプリケーション全体で共通の設定やメソッドを`ApplicationRecord`にまとめることができ、全体の共通設定を一箇所で管理できるようになりました⭐️
また、コードの重複を減らし、保守性の向上にも繋がっています！

:::

## ActiveRecordによるCRUD操作
`ActiveRecord`を利用することで、データベースのCRUD操作（作成、読み取り、更新、削除）を簡単に行うことができるようメソッドが提供されます。
以下にCRUD操作の一例を示します。
SQL文を書くことがなく、直感的にデータベース操作が可能となっていることが分かると思います😳

### 作成（Create）
```ruby
# newメソッドで新しいインスタンスを作成
user = User.new(name: "キー太", email: "qiita@example.com")
# saveメソッドでデータベースに保存
user.save

# createメソッドで新しいレコードを作成して保存
user = User.create(name: "キー太", email: "qiita@example.com")
```

### 読み取り（Read）
```ruby
# すべてのユーザーを取得
users = User.all

# 最初のユーザーを取得
user = User.first

# 主キーでユーザーを取得
user = User.find(1)

# 条件に基づいてユーザーを取得
user = User.find_by(email: "qiita@example.com")
```

### 更新（Update）
```ruby
# ユーザーの属性を更新して保存
user = User.find(1)
user.name = "みかん"
user.save

# updateメソッドで属性を更新して保存
user = User.find(1)
user.update(name: "みかん")
```

### 削除（Delete）
```ruby
# ユーザーを削除
user = User.find(1)
user.destroy
```

# 終わりに
最後まで読んでいただきありがとうございました😌
Railsとは切っても切れない`ActiveRecord`について、理解が深まったと感じています💪
`ActiveRecord`によって実現可能となっている機能がたくさんあるので、そちらも深掘りしていきたいと思います🔥

# 参考記事
https://railsguides.jp/active_record_basics.html

https://qiita.com/ryokky59/items/a1d0b4e86bacbd7ef6e8
