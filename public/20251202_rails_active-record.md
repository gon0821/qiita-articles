---
title: 【Rails初学者】ActiveRecordの基礎についてざっくり理解する
tags:
  - Rails
  - ActiveRecord
  - 初学者
private: false
updated_at: ''
id: null
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


