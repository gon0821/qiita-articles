---
title: 【Rails史】Railsの過去を知る -Part5 | 20年の軌跡（2016年〜2018年）
tags:
  - Rails
  - 歴史
  - 初学者
private: false
updated_at: '2025-12-25T04:49:35+09:00'
id: 183e2098dbbc4e9c4ccf
organization_url_name: null
slide: false
ignorePublish: false
---
# Part4続き
前回の記事では、2013年から2015年までの軌跡を振り返りました📙
今回は2016年から2018年までの`Rails`の軌跡についてまとめていきたいと思います！✍️

https://qiita.com/gon0821/items/88c40f752a01e135fc87

![rails-history.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/889b5cc8-47e3-43f3-8646-d6e347139eb4.png)

# Railsの軌跡 - 第5章
## 2016年：Rails5.0正式リリース
![rails5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/a3a2d70e-3459-4a4d-b9b5-0a2ef2a22bbc.png)

長期間のベータテストを経て、2016年6月に`Rails5.0`が正式リリースされました！ 主な新機能は以下の通りです。

**Rails5.0のリリース**
| 機能 | 説明 |
|---|---|
| **Action Cable** | WebSocketをRailsの流儀で扱えるようになり、チャットやリアルタイム通知などの機能を標準で実装可能に |
| **APIモード** | JavaScriptフレームワーク（React/Vueなど）やモバイルアプリのバックエンドに特化した、軽量なRailsアプリを構築する設定が追加 |
| **Railsコマンドの統合** | それまで`rake`コマンドで行っていた操作（マイグレーション等）が、すべて`rails`コマンド（`rails db:migrate`など）に統合され、利便性が向上 |
| **ApplicationRecordの導入** | すべてのモデルの基底クラスとして`ApplicationRecord`が導入され、共通のロジックや設定を一元管理できるように |

:::note info
**Action Cableとは？**
- WebSocketを利用して、リアルタイム通信を可能にするRailsのフレームワーク
- チャットアプリや通知システムなど、リアルタイム性が求められる機能を簡単に実装できる
- Railsの既存のMVCアーキテクチャとシームレスに統合されているため、学習コストが低い
:::

**Turbolinks5の導入**
- `Rails5`では、`Turbolinks5`が導入され、従来のTurbolinksよりも高度な機能と柔軟性が提供されました。
- `Turbolinks5`は、部分的なページ更新や履歴管理の改善など、より洗練されたユーザー体験を実現しました。

## 2017年：Rails5.1リリースとJavaScriptエコシステムの強化
2017年4月にリリースされた`Rails5.1`では、JavaScriptエコシステムの強化に重点が置かれました⭐️

**Rails5.1リリース**
| 機能 | 説明 |
|---|---|
| **Webpackerの導入** | JavaScriptパッケージ管理ツールの**webpack**をRailsで利用可能にするラッパーが登場しました。これにより、`React`、`Vue`、`Angular`といったモダンなフレームワークの導入が公式にサポートされた |
| **Yarnのサポート** | これまではGemでJSライブラリを無理やり管理していたが、JavaScriptのパッケージマネージャーである**Yarn**が公式にサポートされ、JavaScriptライブラリの管理が容易に |
| **System Testsの導入** | Capybaraを利用したシステムテストが標準でサポートされ、ブラウザを介したエンドツーエンドのテストが簡単に書けるようになった |
| **Encrypted Secrets** | 機密情報を暗号化して管理する仕組みが導入され、セキュリティが強化された |

![yarn.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/04f3b6a0-7363-412a-ab50-233714493511.png)

:::note info
**Webpackerとは？🤔**
- JavaScriptのモジュールバンドラーである**Webpack**をRailsアプリケーションで簡単に利用できるようにするGem
- `app/javascript`ディレクトリを作成し、ここにJavaScriptコードを配置することで、モダンなJavaScript開発が可能に
- 当時、Reactなどの最新JSを使うには**Webpack**というビルドツールが必須であったが、設定が難解で、Railsと組み合わせるのは至難の業だったので、Webpackerの登場は非常に大きな前進となった
:::

**私😮「正直なところ`Webpack`とか`Webpacker`とかJS関連の部分ちゃんと理解できていないけど、`Rails`ができるだけJSライブラリを使いやすくしようと改善してくれていることは分かった🙆」**

## 2018年：Rails5.2リリースとインフラ機能の強化
2018年4月にリリースされた`Rails5.2`では、これまで外部ライブラリに頼っていた多くの機能が標準化されました🔧
**Rails5.2リリース**
| 機能 | 説明 |
|---|---|
| **Active Storageの導入** | Amazon S3やGoogle Cloud Storageなどのクラウドストレージへのファイルアップロード・管理を、モデルから直接扱えるようになった。画像のリサイズ（要ImageMagick等）も標準でサポート |
| **Bootsnapの標準採用** | アプリケーションの起動を劇的に高速化する bootsnap がデフォルトで有効になり、開発時の待ち時間がさらに短縮 |
| **Redis Cache Storeの標準サポート** | Redisをキャッシュストアとして公式にサポートし、高速なキャッシュ運用が可能に |
| **Encrypted Credentials** | 機密情報管理の新しい仕組みが導入され、`config/credentials.yml.enc`に暗号化された形式で保存されるようになった |

**GitHubの買収**
- 2018年6月に`Rails`で構築された世界最大の開発プラットフォームGitHub が、Microsoftによって買収されるという衝撃的なニュースがありました。

![microsoft-github.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/a9230967-c3b5-4012-815c-5c00ccc2d9a6.png)

**DHH氏の主張**
- DHH氏は、この頃からJavaScriptを多用するSPAの流行に対して懐疑的な立場を強め、Rails単体でユーザー体験を完結させることの重要性を再度強調しました。
- これがのちの`Hotwire`や`Stimulus`の登場につながっていきます。

# Part6へ続く
次回は2019年から2021年までの軌跡を振り返ります📙

# 参考記事
https://railsguides.jp/5_0_release_notes.html

https://railsguides.jp/5_1_release_notes.html

https://railsguides.jp/5_2_release_notes.html
