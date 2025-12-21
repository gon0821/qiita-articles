---
title: "【Rails初心者】renderとredirect_toの使い分けをざっくり理解する\U0001F914"
tags:
  - Rails
  - view
  - 初学者
private: false
updated_at: '2025-12-21T16:09:12+09:00'
id: d50946368789c8a8618b
organization_url_name: null
slide: false
ignorePublish: false
---
# ゴール
この記事では、Railsの`render`と`redirect_to`の違いと使い分けについてざっくり理解することを目指します⭐️
それぞれのメソッドがどのような役割を果たしているのかを理解し、適切な場面で使い分けられるようになることを目標とします🙋

![render-redirect_to.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/9f0e280d-673e-4763-be1c-a3b76d14b4ff.png)


# `render`とは？
`render`メソッドは、指定されたテンプレートから`@user`などのRubyコードを評価し、HTMLを生成してレスポンスとして返すために使用されます！

```mermaid
graph LR
    %% ノードの定義
    Input1["📄 <b>new.html.erb</b><br>入力1（設計図）"]
    Input2["👤 <b>@user</b><br>入力2（材料）"]
    Process("⚙️ <b>render</b><br>処理（埋め込み）")
    Output["🌐 <b>HTML</b><br>出力（成果物）"]

    %% 線の接続
    Input1 --> Process
    Input2 --> Process
    Process --> Output

    %% 色付け（見やすくするため）
    style Process fill:#f9f,stroke:#333,stroke-width:2px,color:black
    style Output fill:#bbf,stroke:#333,stroke-width:2px,color:black
```

**`render`のコントローラーでの役割**
Railsコントローラにおいて、`render`は「レスポンスの作成」を意味します！

本来は、コントローラのアクションが終了すると、自動的に対応するビュー（テンプレート）がレンダリングされます。しかし、`render`メソッドを使用することで、特定のテンプレートを明示的に指定したり、異なるテンプレートをレンダリングしたりすることができます。

```ruby
def new
  # render :new が暗黙的に呼ばれる
end

def new
  render :show # newではなく、showテンプレートをレンダリング
end
```

### 処理のイメージ

![render-img.jpeg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/fc2eaa4b-8701-41d8-919c-b53eaf4b2ce7.jpeg)
<sub>参考：[renderとredirect の違い](https://qiita.com/january108/items/54143581ab1f03deefa1)より</sub>

- 現在のリクエスト内でビューをレンダリングし、HTMLを生成してレスポンスとして返す（**新しいリクエストが発生しない**）。そのため、ブラウザのURLも変わらない。
- 同じリクエスト内なので、コントローラで設定したインスタンス変数（`@user`など）をそのままビューで利用できる
- バリデーションエラー時など、データを保持したままフォームを再表示したい場合に便利

# `redirect_to`とは？
`redirect_to`メソッドは別ののURLにリクエストを再送信するようブラウザに指示します！
`render`メソッドではレスポンスを構成するときに使うビュー（または他のアセット）を指定していましたが、この点で根本的に異なります。

```mermaid
graph LR
    Browser["🖥️ ブラウザ<br>（ユーザー）"]
    Controller1["⚙️ createアクション<br>（保存処理が成功したら<br>redirect_to）"]
    Controller2["⚙️ showアクション<br>（表示処理）"]

    %% 1回目のリクエスト（往復）
    Browser -- "① POST<br>（保存依頼）" --> Controller1
    Controller1 -- "② 302 Found<br>（別リクエストを命令）" --> Browser

    %% 2回目のリクエスト（往復）
    Browser -- "③ GET<br>（移動先へアクセス）" --> Controller2
    Controller2 -- "④ 200 OK<br>（新しいHTML返却）" --> Browser

    %% スタイルの定義
    style Browser fill:#bbf,stroke:#333,stroke-width:2px
    style Controller1 fill:#f9f,stroke:#333,stroke-width:2px
    style Controller2 fill:#f96,stroke:#333,stroke-width:2px

```

**`redirect_to`のコントローラーでの役割**
Railsコントローラにおいて、`redirect_to`は「別のURLへのリダイレクト」を意味します！
下記のように使用されることが一般的です。

```ruby
def create
  if @user.save
    redirect_to @user  # ユーザーの詳細ページへリダイレ
  else
    render :new        # バリデーションエラー時はnewテンプレートを
  end
end
```

### 処理のイメージ

![redirect_to.jpeg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/85ed3415-1637-4ce9-8227-0654269bc05a.jpeg)

<sub>参考：[renderとredirect の違い](https://qiita.com/january108/items/54143581ab1f03deefa1)より</sub>

- 新しいリクエストを発生させる（`302`リダイレクト）
- 新しいリクエストになるため、コントローラで設定したインスタンス変数は引き継がれない
- 主に、データの保存や更新が成功した後に、別のページへ移動させるために使用される

# まとめ
|                     | `render`                                    | `redirect_to`                             |
|---------------------|---------------------------------------------|-------------------------------------------|
| 目的                | ビューのレンダリング                        | 別のURLへのリダイレクト                  |
| リクエストの発生    | なし（同じリクエスト内で処理）              | あり（新しいリクエストが発生）            |
| インスタンス変数の利用 | 可能（同じリクエスト内で維持される）          | 不可（新しいリクエストになるため引き継がれない）  |
| 主な使用場面      | バリデーションエラー時のフォーム再表示など    | データ保存・更新後のページ移動など              |

# おわりに
`render`と`redirect_to`は、Railsアプリケーションでビューの表示やページ遷移を制御するための重要なメソッドです💪
それぞれぞれの役割と使い分けを理解し、適切に使い分けることでより効果的に開発を行なっていきたいと思います🧑‍💻

# 参考記事
https://railsguides.jp/layouts_and_rendering.html

https://qiita.com/january108/items/54143581ab1f03deefa1#redirect

https://zenn.dev/take_tech/articles/62468f3f149e5b

