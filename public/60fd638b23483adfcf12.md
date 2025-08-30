---
title: >-
  【Rails7 / ActionCable / 備忘録】「ArgumentError - wrong number of arguments (given
  1, expected 2)」に対するエラー対処（備忘録）
tags:
  - Rails
  - error
  - ActionCable
  - エラー対処
private: false
updated_at: '2023-10-19T10:14:53+09:00'
id: 60fd638b23483adfcf12
organization_url_name: null
slide: false
ignorePublish: false
---


# 開発環境

- Ruby 3.2.2
- Ruby on Rails 7.0.6
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3117662/0c35f0b8-d878-485c-57c2-6468f7d40c17.png)


# エラー内容
ActionCableを用いたチャット機能を実装しようと、以下の実装の手順を書いてくれている記事を参考に進めていたのですが、テキストを送信しても実行されずサーバーログを確認してみると、
**`「ArgumentError - wrong number of arguments (given 1, expected 2)」`** 
となっており、直訳すると「２つの引数が渡されるのを期待しているが、１つの引数しか渡されていない」というエラーが発生していました。
そこでエラー箇所を確認してみるも以下のように`ActionCable.server.broadcast`には、`room_channel`と`message: data['message']`が引数として２つとして渡されているように見えますし、ネットを調べても、ChatGPTに尋ねてもほとんど情報が得られませんでした。
```Ruby:channels/room_channel.rb
class RoomChannel < ApplicationCable::Channel
    # 省略
  def speak(data)
    ActionCable.server.broadcast 'room_channel', message: data['message']
  end
end

```

【参考】実装手順の記事
----
- [【Rails6.0】ActionCableを使用したライブチャットアプリを実装する手順を解説](https://techtechmedia.com/action-cable-rails6/)
- [【Rails6.0】ActionCableとDeviseの欲張りセットでリアルタイムチャット作成(改正版)](https://qiita.com/rhiroe/items/4c4e983e34a44c5ace27)

# 解決方法
- **変更前**
```Ruby:room_channel.rb
ActionCable.server.broadcast 'room_channel', message: data['message']
```
- **<font color=red>変更後</font>**

```Ruby:room_channel.rb
ActionCable.server.broadcast 'room_channel', {message: data['message']}
```

キーにシンボルを利用したハッシュである`message: data['message']`を「{}」で囲みました。
- **変更前**
どうやら変更前の形式では、Rubyはこれを`ハッシュではなくキーワード引数`として解釈していたようです。
そうすると、`ActionCable.server.broadcast`メソッドから見ると２つ目の引数として、データ型がハッシュの値を期待していたにも関わらず、期待しているデータ型ではなかったため`「nil」`と解釈され、引数が１つしか渡されていない状態になったと考えられます。

- **<font color=red>変更後</font>**
明示的に{}で囲むことでハッシュとして認識され、broadcastメソッドには2つの引数が渡されたと解釈したため、エラーが発生せずに正常に動作しました。

# 終わりに

他の方ができているのになぜ今回ここでエラーとなったのか判明できていないですが、Rubyのメソッド呼び出しにおけるキーワード引数とハッシュの記法が似ているために今回のエラーが起きたと考えられます。
今後、ハッシュを引数として渡したい場合は、常に{}で囲むことにより明示的にハッシュであることを示していきたいと思います！
今回のエラー解決にかなり時間がかかったので、同じようなエラーで困っている方の助けに少しでもなればと思います:wink:


# 5. 参考文献

- ググり続けて、こちらの記事よりようやく解決法が得られました。ありがとうございました！

https://zenn.dev/chikaniki2/articles/d941343ca297e5

