---
title: 【Rails史】Railsの過去を知る -Part4 | 20年の軌跡（2013年〜2015年）
tags:
  - Rails
  - 歴史
  - 初学者
private: false
updated_at: '2025-12-25T00:52:09+09:00'
id: 88c40f752a01e135fc87
organization_url_name: null
slide: false
ignorePublish: false
---
# Part3続き
前回の記事では、2010年から2012年までの軌跡を振り返りました📙
今回は2013年から2015年までの`Rails`の軌跡についてまとめていきたいと思います！✍️

https://qiita.com/gon0821/items/e67f0e776aa4bf9cc47c

![rails-history.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/889b5cc8-47e3-43f3-8646-d6e347139eb4.png)

# Railsの軌跡 - 第4章
## 2013年：Rails4.0リリースとTurbolinksの登場
**Rails4.0のリリース**
2013年6月に`Rails4.0`がリリースされました！主な内容は以下の通りです。
| 機能 | 説明 |
|---|---|
| Strong Parameters | 2012年の事件を受け、セキュリティ対策である**Strong Parameters**が標準機能へ。これにより、開発者は「許可したデータ以外は通さない」という安全なコードを自然と書くようになり、Railsアプリの堅牢性が飛躍的に向上した |
| Turbolinksの導入 | 「SPAのように、画面遷移を高速にしたい」。その願いをサーバーサイド主導で叶える**Turbolinks**がデフォルトで搭載された。 JSを駆使しなくても、Railsの規約に従うだけでネイティブアプリのようなサクサク感が出せる。この「Rails Way」なアプローチは、多くの開発者を驚かせた|
| 非同期キューの標準化 | ActiveJob（Rails 4.2で完成）の前段階として、キューイングの仕組みが整理され始めた |
| Ruby2.0への対応 | 2013年2月に登場した Ruby 2.0 をフルサポートし、最新のRubyの機能を活用できるようになった |

:::note info
**Turbolinksって何がすごかったの？？**
- **通常のWeb**（遅い...😑）
  - **リンククリック**　→　**サーバーにリクエスト送信**　→　**サーバーでHTML生成**　→　**ブラウザで再描画**
- **SPA（Reactなど）**(速い！🚀でも作るのが大変😅)
  - **リンククリック**　→　**APIにリクエスト送信**　→　**JSON受信**　→　**JavaScriptで画面更新**
- **Turbolinks**（速い！🚀しかもRailsだけで作れる！😆）
  - **リンククリック**　→　**サーバーにリクエスト送信**　→　**CSS/JSは使い回す**　→　**必要な部分だけ差し替え**

当時、ReactやAngularなどのSPAフレームワークが台頭し始めていましたが、Railsコミュニティでは「もっとシンプルに、Railsだけで高速な画面遷移を実現したい！」というニーズがありました。
Turbolinksはそのニーズに応え、Rails開発者にとって画期的なソリューションとなりました！

<sub>※ ただし、jQueryなどの既存のJavaScriptライブラリとの相性問題があり、多くの開発者を悩ませることにもなりました...😅</sub>

:::

**Dockerの登場**
2013年3月には`Docker`が誕生し、`Rails`アプリケーションの実行環境をコンテナ化して配布する、現在の開発スタイルの基礎がこの年に生まれました。

![docker.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/358d683f-494f-4f5b-a3af-f9e9e447e84d.png)

`Docker`について詳しくは[こちら](https://qiita.com/gon0821/items/2ab56df440e057f9d1b7)をご確認ください👀

https://qiita.com/gon0821/items/2ab56df440e057f9d1b7

## 2014年：Rails4.1&4.2リリース | Rails10周年
**Rails4.1リリース**
2014年4月にリリースされた`Rails4.1`では、開発者の日常的な不満を解消する実用的な新機能が多数導入されました🎉
| 機能 | 説明 |
|---|---|
| **Active Record Enums** | データベースの数値を、プログラム上ではわかりやすい言葉（active, archivedなど）として扱える**Enums**が登場した<br>「`status = 1` って何だっけ？」と悩む必要がなくなり、`user.active?` のように英語を読む感覚でコードが書けるようになった |
| **Action Mailer Previews** | メール送信機能の開発・デバッグを容易にする**Action Mailer Previews**が導入された<br>メールの内容をブラウザでプレビューできるようになり、開発効率が向上した |
| **Spring**<br>(アプリケーションプリローダー) | コマンド実行時のRailsの起動を高速化するツールが標準搭載された<br>テストやマイグレーションなど、頻繁に実行するコマンドの待ち時間が大幅に短縮され、開発者のストレスが軽減された |
| `secrets.yml`の導入 | アプリケーションの秘密情報（APIキーやパスワードなど）を管理するための`secrets.yml`が導入された<br>セキュリティ管理が体系化され、より安全なアプリケーション開発が可能になった |

`enum`について詳しくは[こちら](https://qiita.com/gon0821/items/fa78bf309d75852e7fef)をご確認ください📝

https://qiita.com/gon0821/items/fa78bf309d75852e7fef

**私😌「メールのプレビュー機能はたまにしか使わんけど、パッと確認したいときに便利なんよな」**

**Rails4.2リリース**
2014年12月にリリースされた`Rails4.2`では、以下のような新機能が追加されました！
| 機能 | 説明 |
|---|---|
| **Active Job** | 非同期処理（バックグラウンドジョブ）の標準インターフェースが導入された。これにより、`Sidekiq`などのバックエンドを簡単に切り替えられるようになった|
| **メールの非同期送信** | `Active Job`を利用して、deliver_later メソッドでメール送信を簡単にバックグラウンド化できるようになった |
| **WebConsole** | 開発中のエラー画面上で直接Rubyコードを実行してデバッグできる、インタラクティブなコンソールが標準で統合された |

**Rails10周年🎊**
2004年の誕生から10周年を迎え、シカゴで開催された[**RailsConf 2014**](https://www.rubyevents.org/events/railsconf-2014)では、創設者のDHH氏やYehuda Katz氏らがこれまでの10年を振り返り、将来の展望を語る記念すべき講演が行われました。

![rails-conf.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/4581e639-f8af-4669-aa1f-60688833dd1f.png)

## 2015年：次世代への準備を重ねた年
2015年は、従来の「サーバーサイドでHTMLを生成するRails」という枠を超え、「**リアルタイム**」や「**API**」といったモダンWebの要求に全方位で応える準備を整えた年でした！

**Rails5.0の発表**
2015年の[RailsConfでDHH氏より発表](https://www.rubyevents.org/talks/opening-keynote-railsconf-2015?back_to=%2Fevents%2Frailsconf-2015%3Fscroll_top%3D574&back_to_title=RailsConf+2015)された`Rails5.0`は、コミュニティに大きな期待を抱かせました🥳
| 機能 | 説明 |
|---|---|
| **Action Cable** | 今まで`Node.js`などの外部サービスに頼っていたリアルタイム通信を、Railsだけで実現するためのフレームワークが導入されることが発表された<br>これにより、チャットアプリや通知機能などのリアルタイム機能が簡単に実装できるように！ |
| **APIモード** | それまで外部Gem（`rails-api`）として提供されていたAPI専用モードが公式に統合されることが発表された<br>これにより、フロントエンド（`React/Vue`など）とバックエンドを分離したモダンなアプリケーション開発スタイルに正式対応した |

:::note info
**APIモードって何がいいの？😵‍💫**
従来のRailsとAPIモード、何がどう違うんだろう？？
- **従来のRails**（フルスタックモード）
  - コントローラーはHTMLレスポンスを返すことが前提
  - ビューやアセットパイプラインなど、フロントエンド関連の機能が多数含まれる
- **APIモード**
  - コントローラーはJSONレスポンスを返すことが前提
  - ビューやアセットパイプラインなど、フロントエンド関連の機能は含まれない
  - 軽量で高速なAPIサーバーを構築可能

APIモードを使うことで、フロントエンドとバックエンドを明確に分離した設計が容易になり、モダンなWebアプリケーション開発に最適化された環境が提供されました🎉
:::

**Rails5.0 β版リリース**
2015年12月には`Rails5.0`のβ版がリリースされ、長年使われてきた`rake`コマンドが`rails`コマンドに統合されました。
`rake db:migrate`が`rails db:migrate`と書けるようになったのはこの時からです。

**エコシステムの変化**
- **Reactの台頭**
  - 2013年にFacebookが開発した`React`が急速に普及し始め、`Rails`を「APIサーバー」として使い、フロントエンドを`React`で構築する構成が増え始めた時期でした。
- **Webpackerの登場**
  - `Rails`アプリケーションでモダンなJavaScript開発を容易にするために`Webpack`をどう導入するかが議論され始めました🤔

DHH氏は「The Rails Doctrine（Railsの教義）」を整理し、シングルページアプリケーション（SPA）全盛期にあっても、Railsのフルスタックなアプローチがいかに生産的であるかを改めて強調しました。

https://rubyonrails.org/doctrine/ja#integrated-systems

# Part5へ続く
次回は2016年から2018年までの軌跡を振り返ります📙
だんだんと最近の話題になってきてワクワクしてきました！

# 参考記事
https://railsguides.jp/4_0_release_notes.html

https://railsguides.jp/4_1_release_notes.html

https://railsguides.jp/4_2_release_notes.html
