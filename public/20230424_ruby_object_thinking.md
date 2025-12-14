---
title: 【Ruby】簡単な書籍管理システムを実装しながらオブジェクト指向のイメージを掴もう！
tags:
  - Ruby
  - オブジェクト指向
  - 初学者向け
private: false
updated_at: '2023-04-24T18:31:57+09:00'
id: b662dabac81acd67e173
organization_url_name: null
slide: false
ignorePublish: false
---
# １．はじめに
最近、Rubyでオブジェクト指向を学び始めたのですが、頭の中で整理されておらず苦労した点も多かったので、今回は実際に簡単な書籍管理システムをオブジェクト指向を使用しながら実装し、復習していきたいと思います。


# ２．書籍管理システムを実装していく
今回、作成するシステムのイメージとしては、システムに登録されている書籍のタイトルを入力するとその書籍の情報が表示されるようなシステムにしていきます。
![undraw_Books_re_8gea.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/b9d5a691-ec47-fd1d-37df-0364712d47c2.png)


# （１） クラスの定義
ここでは神戸市の図書館にあるシステムを想定して、実装をしていきます。
クラスはLibraryクラスとBookクラスを作成し、以下のような役割のイメージを持ちます。
|クラス|役割|属性・データ|メソッド・振る舞い|
|:--:|:--:|:-------|:----------|
| Library|システムとしての動作|<ul><li>システムに登録された書籍</li></ul>|<ul><li>書籍の新規登録</li><li>書籍のリスト表示</li><li>書籍の検索</li></ul>|
|Book|書籍情報の把握|<ul><li>タイトル名</li><li>著者名</li><li>発行年</li></ul>|<ul><li>書籍情報を表示</li></ul>|
### Libraryクラス
* 書籍管理システムのLibraryクラスを宣言する。
* 検索ボタンを押すと、書籍が表示されるようにする。
ここではsearch_buttonメソッド実行すると、「半沢直樹」が表示されるようにしていきましょう！
（半沢直樹のドラマにハマりすぎて、ドラマが終わってしまったときは半沢直樹ロスになっていました...笑）
```ruby:クラス定義
class Library
    def search_button
        '半沢直樹'
    end
end
```
```ruby:呼び出し
kobe_library = Library.new
kobe_library.serch_button
```
```:出力画面
半沢直樹
```

### Bookクラス
* 書籍情報が入っているBookクラスを宣言する。
* infoメソッドを実行すると、タイトル・著者・発行年が表示されるようにする。
```ruby:クラス定義
class Book
    def info
        "タイトル：半沢直樹, 著者：池井戸潤, 発行年：2004"
    end
end
```
```ruby:呼び出し
book1 = Book.new
book1.info
```
```:出力画面
タイトル：半沢直樹, 著者：池井戸潤, 発行年：2004
```

# （2） initializeメソッドを追記
インスタンスを初期化するために実行したい処理をinitializeメソッドに実装していきます。
### Libraryクラス
* システムに書籍を登録していくために空配列が用意されたbooks変数を定義する。
* books変数は同じインスタンスの内部で共有して使用したいので、「@」を先頭に付けインスタンス変数として定義する。
```ruby:クラス定義
class Library
    def initialize
        @books = []
    end
end
```
### Bookクラス
* インスタンス生成時にその本のタイトル・著者・発行年をインスタンス変数に持たせる。
本によってデータが変わるためinitializeメソッドに引数をつけ、newメソッドでインスタンスを生成するときにも引数が必要となることに注意する。
* infoメソッドをインスタンス変数を用いて定義する。

```ruby:クラス定義
class Book
    def initialize(title, author, publication_year)
        @title = title
        @author = author
        @publication_year = publication_year
    end

    def info
        "タイトル：#{@title}, 著者：#{@author}, 発行年：#{@publication_year}"
    end
end
```
```ruby:呼び出し
book1 = Book.new('半沢直樹', '池井戸潤', 2004)
book1.info
```
```:出力画面
タイトル：半沢直樹, 著者：池井戸潤, 発行年：2004
```

# （3） アクセスメソッドの追記
**attr_reader**メソッドを用いて、インスタンス変数へのアクセス（取得）を簡単にしておきましょう。
また、今回は読み込みのみを想定しているためattr_readerメソッドですが、外部からインスタンス変数の内容を変更できるようにしたい場合は**attr_writer**メソッド、読み込み書き込みの両方できるようにしたい場合は**attr_accessor**メソッドを用います。
### Libraryクラス
```ruby:クラス定義
class Library
    attr_reader :books
    # 省略
end
```

### Bookクラス
試しにインスタンス変数の＠authorに何が入っているか読み込んでみましょう。
```ruby:クラス定義
class Book
    attr_reader :title, :author, :publication_year
    #　　省略
end
```
```ruby:呼び出し
book1 = Book.new('半沢直樹', '池井戸潤', 2004)
book1.author
```
```:出力画面
池井戸潤
```
きちんと読み込めていました(*^^*)

# （4）【機能①】新しい書籍をシステムへ追加する 
Bookクラスで作成されたインスタンスをLibraryクラスのインスタンス変数＠booksへ追加していくadd_bookメソッドをここで追加していきましょう。
### Libraryクラス
* add_bookメソッドの引数にBookクラスで作成されたインスタンスを渡し、空配列の＠booksに追加していく。
```ruby:クラス定義
class Library
    # 省略
    def add_book(book)
        @books << book
    end
end
```
```ruby:呼び出し
kobe_library = Library.new
book1 = Book.new('半沢直樹', '池井戸潤', 2004)
# 本の追加
kobe_library.add_book(book1)
kobe_library.books
```
```:出力画面
#<Book:0x0000000102509288>
```
出力画面にはBookクラスのインスタンスが表示されているので、add_bookメソッドによりインスタンス変数＠booksに追加されていることが確認できました！

# （5）【機能②】システム上の全ての書籍を表示する
次にシステムに登録されている全ての書籍を表示させるlist_booksメソッドを追加していきます。
### Libraryクラス
* list_booksメソッドでは配列＠booksをeachメソッドを用いて順番に要素を取り出しながらbookのinfoメソッドで書籍情報を表示させる。
```ruby:クラス定義
class Library
    # 省略
    def list_books
        @books.each { |book| puts book.info }
    end
end
```
```ruby:呼び出し
kobe_library = Library.new
book1 = Book.new('半沢直樹', '池井戸潤', 2004)
book2 = Book.new('君の膵臓をたべたい', '住野よる', 2015)
# 本の追加
kobe_library.add_book(book1)
kobe_library.add_book(book2)
# 全ての本を表示
kobe_library.list_books
```
```:出力画面
タイトル：半沢直樹, 著者：池井戸潤, 発行年：2004
タイトル：君の膵臓をたべたい, 著者：住野よる, 発行年：2015
```
出力画面にadd_bookメソッドで登録した書籍が表示されているので、list_booksメソッドの挙動が正しいことが確認できました。

# （6）【機能③】書籍名で検索できるようにする
最後に書籍名だけ把握している場合に、その書籍の情報を検索できるようなfind_bookメソッドを実装していきましょう。
### Libraryクラス
* find_bookメソッドでは、配列＠booksをfindメソッドを用いて順番に要素を取り出しながら、bookのインスタンス変数titleが引数のtitleと一致したときに戻り値を真で返し、そのときの最初の要素を取り出す。
* find_bookメソッドの戻り値はBookクラスのインスタンスであるので書籍の情報を表示させるinfoメソッドの使用が可能である。
```ruby:クラス定義
class Library
    # 省略
    def find_book(title)
        @books.find { |book| book.title = title }
    end
end
```
```ruby:呼び出し
kobe_library = Library.new
book1 = Book.new('半沢直樹', '池井戸潤', 2004)
book2 = Book.new('君の膵臓をたべたい', '住野よる', 2015)
# 本の追加
kobe_library.add_book(book1)
kobe_library.add_book(book2)
# タイトルだけ分かっている書籍を検索
found_book = kobe_library.find_book('君の膵臓をたべたい')
puts "検索結果→#{found_book.info}"
```
```:出力画面
検索結果→タイトル：君の膵臓をたべたい, 著者：住野よる, 発行年：2015
```
出力画面に検索結果が表示されましたので、無事実装できました！

# ３．おわりに
オブジェクト指向を学び始めましたが、雲を掴むような話でなかなか理解できておらず知識も浅いですが、今回のように何かサービスを作りながら学ぶのが頭に入ってきやすいなと感じました(^^)
オブジェクト指向にはここに載せれてない技術が他にもたくさんありますので、そちらも地道に勉強して慣れていきたいです！

# ４．参考図書
* [プロを目指す人のためのRuby入門](https://www.amazon.co.jp/%E3%83%97%E3%83%AD%E3%82%92%E7%9B%AE%E6%8C%87%E3%81%99%E4%BA%BA%E3%81%AE%E3%81%9F%E3%82%81%E3%81%AERuby%E5%85%A5%E9%96%80-%E8%A8%80%E8%AA%9E%E4%BB%95%E6%A7%98%E3%81%8B%E3%82%89%E3%83%86%E3%82%B9%E3%83%88%E9%A7%86%E5%8B%95%E9%96%8B%E7%99%BA%E3%83%BB%E3%83%87%E3%83%90%E3%83%83%E3%82%B0%E6%8A%80%E6%B3%95%E3%81%BE%E3%81%A7-Software-Design-plus%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA/dp/4774193976)
