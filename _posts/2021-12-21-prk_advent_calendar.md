---
layout: post
title:  "実質Rubyのキーボードを手に入れるまで(後編)"
date:   2021-12-21 22:00:00 +0900
tags: [prk_firmware, keyboard, diary]
---

この記事は、[PRK Firmware Advent Calendar 2021](https://adventar.org/calendars/7086) 21日目の記事です。

昨日は、@takkanm さんのタイトルが適当な 「[この年末年始はPRK firmware の動くキーボードを作って、新年のスタートでライバルと差をつけよう](https://zenn.dev/takkanm/articles/7a6575926448a9)」でした。  
まだまだ `keymap.rb` と格闘している私は、差がつけられるのか！?

さてさて、前編に残した課題です。

~~先に謝っておくと、今回もgistです!!! キーボード動かすようにしてたら、そっちまで手が回らなかった〜!
年末年始にやります。~~  
(12/28追記 blogに掲載し直しました)

### Nomu30 の続き

さてさて、前編、Pro Micro逆じゃん... で終わってしまったNomu30の続きをやります！

#### 追加購入したもの

* コンスルー 3.5mm
  * Pro Mircoを逆向きにすると、、、コンスルー2.5mm だと高さがたりないため、 Daily Craft Keyboardさんの[ここ](https://shop.dailycraft.jp/products/conthrough_12_35)から買いました。
* 黄銅スペーサー（六角）M2
  * コンスルー 3.5mm にすると、5mm のスペーサーだと引っかかってしまうため、遊舎工房さんの[ここ](https://shop.yushakobo.jp/products/a0800r2)から買いました。  
  実は今日「スペーサー高さ足りないじゃん…」となったので、まだ届いていません... 

#### 再び組み立てて、keymap.rbを確認

はんだを外して、正しい向きに直して、作っておいたdefault layerのみで確認したところ、
あっさり、何か入力できるようになりました。

が、、、どうもキーと出力されるのがあっていないので、`keymap.rb` をゴニョゴニョして、
合わせてレイヤーの切り替えも行い、Ruby で Nomu30 が動くようになりました。

作った `keymap.rb` は [ここ](https://github.com/emorima/prk_nomu30) においてあります！

いぇぇぇーい。なんとかできた！

### Zinc の続き

ちょっとキー全てのはんだ外す時間がなかったので、再組み立ては年末年始に持ち越しします。

ひとまず動かす両方繋いで動きを確認してみたところ、自作 `keymap.rb` では右側のキーが逆になっていました。

~~これは、[ptwrk_firmware 入門](https://zenn.dev/takkanm/articles/91c6741a4a3f26) で @takkanmさんが既に書いてあるように、
右側のキーマップはreverseかけないといけないです。~~

~~keymap は、 @takkanm さんのを参考にした方がよいです。~~
追記：以下の設定でreverseしなくてもできるようになっていました！
```
kbd.split_style = :right_side_flipped_split
```

以下を設定を追加すると、LEDが光ります!

```ruby
rgb = RGB.new(
  0,    # pin number
  0,    # size of underglow pixel
  32,   # size of backlight pixel
  false # 32bit data will be sent to a pixel if true while 24bit if false
)
sleep 1
rgb.effect = :swirl
kbd.append rgb
```

### まとめ

年末年始の持ち越し課題も残ってしまいましたが、ひとまず、Rubyで動くキーボードが２つできました！
やった！嬉しい！しかも光るぞ！

年末年始はRubyで醸したお酒でも飲みつつ、更にRubyキーボードについて、色々触っていきたいと思います！

 明日は、@eswai さんの「PRK firmwareをbuildする」 です！楽しみ！
