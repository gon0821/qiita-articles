---
title: 【Rails / ActiveRecord】find / find_by / where の違いをざっくり理解する
tags:
  - Rails
  - ActiveRecord
  - 初学者向け
  - Rails7
private: false
updated_at: '2024-10-13T13:48:05+09:00'
id: 427271c1e95116cf4464
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
お疲れ様です！
コンソールでデータ取得する際に`find`, `find_by`, `where`をなんとなく使用しており、使い分けについて説明することができなかったので、ざっくりまとめていきたいと思います:writing_hand:

![qiita_find.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/23df6702-8d94-0c81-4a9d-0bb49cc8b8ab.png)

先に3つの違いを一覧をまとめた上で、それぞれの特徴を書いていきます:sunglasses:

# 違いまとめ

| メソッド　　　　     | 用途                                   | 返り値                | 条件に合わない場合 |
|-------------|----------------------------------------|-----------------------|--------------------|
| `find`      | 主キー（ID）で検索       | 単一のレコード | 例外を発生 (ActiveRecord::RecordNotFound) |
| `find_by`   | 指定した条件で単一のレコードを検索 | 最初に見つかった単一のレコード | `nil` を返す        |
| `where`     | 指定した条件で複数のレコードを検索   | ActiveRecord::Relation (配列状のオブジェクト) | 空のリレーションを返す |


## find
- 用途
    -  主キー（ID） で特定のレコードを1つだけ取得するために使用
- 返り値
    - 見つかった場合　　　：そのレコードオブジェクトを返す
    - 見つからなかった場合：例外（`ActiveRecord::RecordNotFound`） が発生
- 特徴
    - 主キー（ID）で検索するため、検索速度が速い
    - 存在しないIDを検索すると、エラーが発生
---

**【ケース１】** 主キーに対応するものを返す
```pry
pry(main)> User.find(1)

=> #<User:0x0000ffff98af47c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>
```

**【ケース２】** 主キーに対応するものがない場合は、エラーが発生
```ruby
pry(main)> User.find(12345)

ActiveRecord::RecordNotFound: Couldn't find User with 'id'=12345
```

**【ケース３】** 主キーに配列を渡した場合は、マッチするレコードを全て含む配列を返す
```pry
pry(main)> User.find([1, 2])

=> [#<User:0x0000ffff98a4d4c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>,
 #<User:0x0000ffff98a4d3f8
 id: 2,
 email: "test-user-2@example.com",
 age: 28,
 created_at: Tue, 27 Aug 2024 18:54:35.492450000 JST +09:00,
 updated_at: Tue, 27 Aug 2024 18:57:11.529007000 JST +09:00>]
```


## find_by
- 用途
    -  任意のカラム に対して**1つ**のレコードを取得するために使用
    -  複数の条件で検索する場合にも使用可能
- 返り値
    - 見つかった場合　　　：最初に見つかったレコードオブジェクトを返す
    - 見つからなかった場合：`nil`を返す
- 特徴
    - 一件だけレコードを取得したい場合に便利
    - 例外処理が不要な場合に使いやすい
---

**【ケース１】** 主キー以外で検索する
```pry
pry(main)> User.find_by(email: "test-user-1@example.com")

=> #<User:0x0000ffff98af47c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>
```

**【ケース２】** 複数条件を指定して検索する
```pry
pry(main)> User.find_by(email: "test-user-1@example.com", age: 28)

=> #<User:0x0000ffff98af47c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>
```

**【ケース３】** 条件に合わない場合は`nil`を返す
```pry
pry(main)> User.find_by(email: "test-user-12345@example.com")

=> nil
```

**【ケース４】** 複数条件指定した場合に、1つでも合わない場合は`nil`を返す
```pry
pry(main)> User.find_by(email: "test-user-1@example.com", age: 100)

=> nil
```


## where
- 用途
    -   複数のレコードを取得するために使用
    -   検索結果に対して追加のクエリやソートなどを行いたいときにも有用
- 返り値
    - 見つかった場合　　　：`ActiveRecord::Relation`オブジェクトを返す
    - 見つからなかった場合：空のリレーションを返す
- 特徴
    - `ActiveRecord::Relation`を返すため、メソッドチェインを使用可能
    - クエリの実行は遅延される（実際にデータが必要になるまでデータベースへの問い合わせを行わない）ので、複数の条件を組み合わせても効率的
---

**【ケース１】** 指定する条件のレコードを全て返す（`ActiveRecord::Relation`を返している）
```pry
pry(main)> User.where(age: 28)

=> [#<User:0x0000ffff98a4d4c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>,
 #<User:0x0000ffff98a4d3f8
 id: 2,
 email: "test-user-2@example.com",
 age: 28,
 created_at: Tue, 27 Aug 2024 18:54:35.492450000 JST +09:00,
 updated_at: Tue, 27 Aug 2024 18:57:11.529007000 JST +09:00>]


pry(main)> User.where(age: 28).class

=> User::ActiveRecord_Relation
```

**【ケース２】** 条件に合わない場合は空のリレーションを返す
```pry
pry(main)> User.find_by(email: "test-user-12345@example.com")

=> nil


pry(main)> User.find_by(email: "test-user-12345@example.com").class

=> User::ActiveRecord_Relation
```

**【ケース３】** NOT条件（条件に一致しないものを返す）
```pry
pry(main)> User.where.not(age: 28)

=> [#<User:0x0000ffff98af47c0
 id: 3,
 email: "test-user-3@example.com",
 age: 40,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>]
```

**【ケース４】** OR条件（複数条件のうちいずれかを満たすものを返す）
```pry
pry(main)> User.where(email: "test-user-1@example.com").or(User.where(age: 40))

=> [#<User:0x0000ffff98a4d4c0
 id: 1,
 email: "test-user-1@example.com",
 age: 28,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>,
#<User:0x0000ffff98af47c0
 id: 3,
 email: "test-user-3@example.com",
 age: 40,
 created_at: Mon, 26 Aug 2024 13:33:47.008672000 JST +09:00,
 updated_at: Thu, 10 Oct 2024 20:01:39.364167000 JST +09:00>]
```


## 補足
以下の説明が分かりやすかったので引用（[引用元の記事](https://qiita.com/YNYS/items/9e0131d110fdf61b42e8)）
### find
`find` は、特定のレコードを取得するためのメソッドなんや。
指定したIDに欲しい情報が入っていることを知っているから使うことになる。
情報が入っているはずで取得したのに、それが無かったから例外になるよな:thinking:

### find_by
`find_by` は、条件を指定した検索メソッドなんやけど、検索結果があるかないかは検索してみないと分からん。
やから結果がないことはあり得ることで、例外ではない:ok_hand:
検索結果が無かったから結果通り`nil`を返す。

### where
`where` は、`find_by` と同様に検索メソッドになる。
違いは、検索にマッチするすべてのレコードを取得できるということやな。
これも検索結果があるかないかは、検索してみないとわからないから、結果がないことは例外ではないな:thumbsup_tone2:
検索結果が0件だから、0件の配列を返す。


# 終わりに
記事にまとめることで3つの違いについて理解を深めることができました！
調べていくうちに理解できていない部分なども新たに発見することができましたので、そちらについても調べて記事にできたらなと思います！
調べたことを自分用にNotionにメモって終わっているものが結構あるので、人に説明できるレベルにするためにも月に１回は記事でアウトプットできるように個人学習頑張りたいと思います:sunglasses:
最後まで読んでいただき、ありがとうございました:four_leaf_clover:



# 参考記事
https://railsguides.jp/active_record_querying.html

https://qiita.com/nakayuu07/items/3d5e2f8784b6f18186f2

https://qiita.com/YNYS/items/9e0131d110fdf61b42e8
