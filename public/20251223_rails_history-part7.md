---
title: 【Rails史】Railsの過去を知る -Part7 | 20年の軌跡（2022年〜2024年）
tags:
  - Rails
  - 歴史
  - 初学者
private: false
updated_at: '2025-12-25T08:13:59+09:00'
id: 17499fd4bd8522f01210
organization_url_name: null
slide: false
ignorePublish: false
---
# Part6続き
前回の記事では、2019年から2021年までの軌跡を振り返りました📙
今回は2022年から2024年までの`Rails`の軌跡についてまとめていきたいと思います！✍️

https://qiita.com/gon0821/items/183e2098dbbc4e9c4ccf

![rails-history.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/889b5cc8-47e3-43f3-8646-d6e347139eb4.png)

# Railsの軌跡 - 第7章
## 2022年：Rails7.0の普及
`Rails7.0`で導入された「ビルドツールを使わない開発」が実際のプロジェクトで試され、その利便性が証明されました。
- **Import Maps の定着**
  - Node.jsやWebpackをインストールせずにJavaScriptを扱う「No Build」スタイルが、小〜中規模の開発において圧倒的なスピードをもたらしました。
- **Hotwire の実践**
  - Turbo と Stimulus を使い、SPA（React等）に匹敵するリッチなUIをサーバーサイド中心で構築する手法が注目されました。
---
**DHH氏からの発表**
- **Propshaftの登場**
  - 重厚なアセットパイプライン（Sprockets）に代わる、非常にシンプルでモダンなアセット管理ツール Propshaft が発表されました
- **クラウドからの脱却**
  - 自社サービス（Basecamp/HEY）をクラウド（AWS）から自前サーバーへ移行する「Cloud Exit」を宣言し、大きな議論を呼びました。これが2023年に発表されるデプロイツール「Kamal」の開発に直結しました。

## 2023年：Rails7.1リリースとKamalの登場
- `Rails7.1`は2023年10月にリリースされ、開発者の生産性を高める実用的な機能が導入されました⭐️
- これまで高度な知識が必要だった「Docker環境の構築」が、Railsの標準機能になりました。 `rails new`するだけですぐに使える最適化された `Dockerfile`が手に入ります。
- また、DHH氏が提唱する「Cloud Exit（クラウド脱却）」を実現するためのツール**Kamal**が正式にリリースされました。
Dockerさえ動けば、AWS、GCP、または月額数ドルの安価なVPSでも、ゼロダウンタイムで簡単にRailsアプリをデプロイできるようになり、複雑なPaaS（Heroku等）や高価なマネージドサービスに頼らない、「独立した開発者」というRailsの理想が再提示されました！

https://railsguides.jp/7_1_release_notes.html

## 2024年：Rails7.2&8.0リリース
- 2024年8月に`Rails7.2`、11月に`Rails8.0`がリリースされました！

**Rails7.2リリース**
| 機能 | 説明 |
|---|---|
| **Dev Containerのサポート** | アプリケーションでDev Container設定を生成する機能が追加され、開発環境のセットアップがさらに簡単に |
| **PWAファイルがデフォルトに** | 新規Railsアプリ作成時にPWA（Progressive Web App）関連のファイルが自動生成され、PWA対応が容易に |

https://railsguides.jp/7_2_release_notes.html

**Rails8.0リリース**
| 機能 | 説明 |
|---|---|
| **Propshaftの標準化** | Propshaftが正式にRailsのデフォルトアセットパイプラインとなり、シンプルで高速なアセット管理が可能に |
| **SQLiteの強化** | SQLiteが大幅に強化され、小規模から中規模のアプリケーションでの利用がさらに促進された |

https://railsguides.jp/8_0_release_notes.html

Rails誕生から20年が経過し、DHH氏が掲げる「シンプルさ」と「独立した開発者」の理念が再び強調されました。Railsは、これからも進化を続け開発者にとって最適なフレームワークであり続けるはずなので、楽しみにしRails開発者の方々に感謝し、これからも使っていきたいと思います😌


