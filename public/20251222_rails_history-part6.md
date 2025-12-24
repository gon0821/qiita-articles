---
title: 【Rails史】Railsの過去を知る -Part6 | 20年の軌跡（2019年〜2021年）
tags:
  - Rails
  - 歴史
  - 初学者
private: false
updated_at: '2025-12-25T06:28:57+09:00'
id: 652a54936166a6dca63a
organization_url_name: null
slide: false
ignorePublish: false
---
# Part5続き
前回の記事では、2016年から2018年までの軌跡を振り返りました📙
今回は2019年から2021年までの`Rails`の軌跡についてまとめていきたいと思います！✍️

https://qiita.com/gon0821/items/183e2098dbbc4e9c4ccf

![rails-history.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/889b5cc8-47e3-43f3-8646-d6e347139eb4.png)

# Railsの軌跡 - 第6章
## 2019年：Rail6.0リリースとZeitwerkの導入
2019年8月に`Rails6.0`がリリースされました！ 主な新機能は以下の通りです。
**Rails6.0のリリース**
| 機能 | 説明 |
|---|---|
| **Webpackerがデフォルトに** | それまでのアセットパイプラインに代わり、WebpackベースのJavaScript管理が標準となった。これにより、モダンなJavaScriptフレームワークの利用が容易に |
| **Action Textの導入** | リッチテキストエディタを簡単に実装できる`Action Text`が追加された。複雑な装飾テキストの保存・表示が数行のコードで実現可能に |
| **Action Mailboxの導入** | 受信メールをRailsアプリ内で処理できる`Action Mailbox`が追加され、メールベースのワークフロー構築が容易に |
| **Multiple Databaseのサポート** | 複数のデータベースを簡単に扱えるようになり、リードレプリカの利用やデータベース分割が容易に |
| **Zeitwerkの導入** | 新しいコードローダーである`Zeitwerk`がデフォルトとなり、コードの自動読み込みがより堅牢かつ柔軟に |

:::note info
**Zeitwerkとは？🤔**
- Rubyのコードローダーで、Rails6からデフォルトで採用された
- ファイル名とクラス名の規約に基づいて自動的にコードを読み込む
- Railsは「ファイル名からクラス名を探す」機能を持っているが、以前の仕組み（`Classic`モード）は少しバグっぽかったり、予期せぬエラーが出たりすることがあった
- `Zeitwerk`はこれらの問題を解決し、より一貫性のあるコード読み込みを実現🙆
:::

**Stimulusの普及**
- DHH氏らが提唱した軽量JavaScriptフレームワーク**Stimulus**が、重厚なSPAへの対抗軸としてRails開発者の間で急速に普及しました。

## 2020年：Rails6.1リリースと改善の年
2020年12月にリリースされた`Rails6.1`では、開発者体験とパフォーマンスの向上に焦点が当てられました⭐️
**Rails6.1リリース**
| 機能 | 説明 |
|---|---|
| **水平スケーリングの強化** | Rails6.0で導入された複数データベースサポートが強化され、水平スケーリングがより容易に |
| **Active Storageの改善** | 複数のサービス（S3、Google Cloudなど）を同時に扱えるようになり、ファイル管理の柔軟性が向上 |
| **strict_loading**の導入 | N+1問題を未然に防ぐため、関連モデルの読み込みを強制的に制限する機能が登場 |

**Hotwireの発表**
- DHH氏率いる37signalsが、**Hotwire** (HTML Over The Wire) を電撃発表しました。
- **Turboの登場**
  - かつてのTurbolinksを置き換える**Turbo**がリリースされました。これにより、SPA（React等）を使わなくても、サーバー側で生成したHTMLを部分的に更新するだけで、非常に滑らかで高速なユーザー体験を実現できる道が示されました。
- **Railsの原点回帰**
  - 「JavaScriptを大量に書くのではなく、HTMLをサーバーから送る」というRails本来の哲学を現代技術で再定義したもので、コミュニティに大きな衝撃を与えました。

https://zenn.dev/shita1112/books/cat-hotwire-turbo/viewer/abstract

## 2021年：Rails7.0リリースとWebpackerからの脱却
![rail7.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/21b23aa2-021b-4ce7-947d-a27d3aca0dda.png)
2021年12月にリリースされた`Rails7.0`は、JavaScript管理の大幅な刷新と開発者体験の向上を目指しました🎉
**Rails7.0のリリース**
| 機能 | 説明 |
|---|---|
| **Importmapの導入** | Webpackerに代わる新しいJavaScript管理手法として**Importmap**が導入され、Node.jsやWebpackを使わずにJavaScriptライブラリを直接ブラウザで読み込むことが可能に |
| **Hotwireの公式サポート** | Hotwire（TurboとStimulus）が公式にサポートされ、SPAのような体験をRailsだけで実現可能に |
| **Active Recordの暗号化** | データベースレベルでのカラム暗号化が標準機能として導入され、個人情報などの管理がより安全に |

DHH氏はRails7のリリースに際し、「**The One Person Framework**」と題したブログ記事を公開しました。
巨大なテック企業がやっているような「専任のフロントエンドチーム、バックエンドチーム、インフラチーム」がいなくても、「**Railsを使えば、たった一人のエンジニアでも、それらに匹敵する高機能なアプリが作れる**」という力強いメッセージとなっています。

https://world.hey.com/dhh/the-one-person-framework-711e6318

**私😌「Rails7でImportmapが導入されたことで、JavaScriptの複雑なビルドツールから解放されて、よりシンプルに開発できるようになったのはありがたい🙆‍♂️」
「てかほんまにフロントエンドは移り変わりが激しいな😇」**

# Part7へ続く
次回は2022年から2025年までの軌跡を振り返ります📙

# 参考記事
https://railsguides.jp/6_0_release_notes.html

https://railsguides.jp/6_1_release_notes.html

https://railsguides.jp/7_0_release_notes.html
