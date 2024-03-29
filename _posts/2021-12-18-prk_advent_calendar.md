---
layout: post
title:  "実質Rubyのキーボードを手に入れるまで(前編)"
date:   2021-12-18 12:00:00 +0900
tags: [prk_firmware, keyboard, diary]
---

当初は、この記事は実質Rubyのキーボード(PRK Firmware)で書いて、
「実質Rubyのキーボードは最高だよ。みんなも作ろうぜ！」的なことを書く予定だった...  
~~そして、[ブログ](https://emorima.love) に掲載する予定だったけど、当日push したらbuild failureで移動までに解決できなかった...~~  
(12/28追記 解決しました) 


この記事は、[PRK Firmware Advent Calendar 2021](https://adventar.org/calendars/7086) 18日目の記事です。

昨日は、 @e3w2q さんの「[PRK Firmwareのドキュメントを充実させる方法](https://e3w2q.github.io/21/)」でし
た。ドキュメントが充実されると嬉しいです!!!

[RubyKaigi Takeout 2021](https://rubykaigi.org/2021-takeout/presentations/hasumikin.html) で、日本酒をRubyで醸せるようにした @hasumikin さんが、今度はRubyでキーボードも動かせるようにしたという発表があったの
で、さっそくmeishi2 を組み立てたところ、動かすことができました。
(自分でコンパイルしようとして詰まったけど、バイナリそのままおいたら簡単にできました)

meishiの次は普段使うキーボードをRubyにしてみようということで、Zinc(家用)とNomu30(旅行用)を作ってみるこ>とにしました。

### Zinc

#### 準備したもの

* [Zinc キーボードキット クリアマット](https://booth.pm/ja/items/1451314)
* [LED](https://shop.yushakobo.jp/products/sk6812mini-e-10)
* [Pro Micro RP2040](https://www.marutsu.co.jp/pc/i/40719713/)
* [コンスルー ＸＢ－３－６－６Ｐ](https://akizukidenshi.com/catalog/g/gC-15596/)
* キースイッチ (家にあった潤滑済みの青軸)
* キーキャップ (家にあったもの)

#### 組み立て1

[ビルドガイド](https://www.sho-k.co.uk/tech/735.html)を見ながら、組み立てます。
初めて組み立てる時は、他の人が組み立てた時のブログも参考になると思います。(記録大事!)

* ダイオードをはんだで取り付ける
* LEDチップをはんだで取り付ける
* Pro Micro RP2040をコンスルーで取り付ける

ここまでできれば、`keymap.rb` の作成して、LEDがつくかの確認をしていきます。  

#### keymap.rbを作成・LEDの確認

@takkanm さんが[Zinc rev1のkeymap.rbを公開](https://gist.github.com/takkanm/bf937b83c67c42d801a31af20132d244)してくれています。  
でも、今回は参考程度にして、自分で `keymap.rb` を書いてみたいっ!!!

他のキーボードのkeymap.rbや、@takkanm さんの [prk_keymap_generator](https://github.com/takkanm/prk_keymap_generator) 、3日目の @e3w2q さんの[PRK Firmwareでの設計メモ](https://e3w2q.github.io/19/)を参考にゴ>ニョゴニョしたものが[こちら](https://gist.github.com/emorima/6d15499f16a9d943c9da6304291213a6#file-keymap-rb)です。

`keymap.rb` でLEDが設定できて光った時は嬉しかった!!!   
のですが、最初の1個しかつかなかった...  
2個めのLEDのはんだが、きちんとついてなかったのが原因でした。

LEDが全て光ったら、LEDの確認は完了です。

#### 組み立て2

* TRRSジャックとリセットスイッチをはんだで取り付ける
* キースイッチもはんだで取り付ける

ケーブル繋いで動作確認して、ケースを組み立てれば!!!! というところで、スペーサーを入れ忘れていることに気
づいたので、キースイッチを一度全部外さねばならない...

後編に続く!!!

### Nomu30

#### 準備したもの

* [Nomu30](https://shop.yushakobo.jp/products/nomu30kit)
* [Pro Micro RP2040](https://www.marutsu.co.jp/pc/i/40719713/)
* キースイッチ(家にあった潤滑済みのクリア軸)
* キーキャップ (家にあったもの)

#### 組み立て1

Nomu30も[ビルドガイド](https://keys.recompile.net/docs/nomu30-build-guide/)を見ながら、組み立てます。

* ダイオードをはんだで取り付ける
* Pro Micro RP2040をコンスルーで取り付ける
* リセットスイッチをはんだで取り付ける
* スタビライザーを取り付ける
* スペーサーを取り付ける

#### keymap.rbを作成

QMK Firmware の設定をもとに、まずはdefalt layerだけでも...と作り始めたところ、
MATRIX_ROW_PINS の値の `B0` の変換値がわからない…
自分なりに色々調べたり、試したりしてみたけど、動く気配がありません。

ひとまず、QMK Firmwareで作っておいて、島根に来て @hasumikin さんに相談してみたところ、
設定をみている keymap.cが違っていた...(と @takkanm さんが教えてくれた。ありがとうございます)

「正しい keymap.rb できたぞ〜」と思っても、やっぱり動かない...
@hasumikin さんが見てくれたら、「これ、Pro Microの向き逆じゃ...」

！！！！

後編に続く!!!


### 前編の中で詰まったところ

* Zincの `keymap.rb` を書いている時に、[QMK Firmware keymap.c](https://github.com/qmk/qmk_firmware/blob/master/keyboards/zinc/keymaps/default/keymap.c) のように、
  キー配列をコメントで記載していたのですが、何度やっても書き込みでエラーになってしまいました。
  ファイルサイズが大きくなってしまったのが原因のようでした。
* 回路もよくわかっていないし、まだ雰囲気でkeymap.rbを書いているので、「これか？これで動くのか？」という
状態です。

### 前編の感想と後編の予定

* とりあえずはんだ外して、Rubyで動くようにします。
  * はんだシュッ太郎にはお世話になりっぱなし。シュッ太郎、ありがとう。
* 動作確認できたら、keymap.rb のリポジトリをpushする予定です。
* そして後半編はちゃんとブログに書く予定です。
* 久しぶりにQMK Firmwareでの環境で書き込みしたけど、前に作っていた頃と環境構築の方法が変わっていて、ち>ょっと戸惑いました。(qmk コマンドができていた...)
* QMK Firmware は、キー配列を少し修正して使う使い方しかしていないので、`keymap.rb` を自分で書くことで、
「何の設定が必要なのか」が見えやすいなぁ...と感じています。
  このあたり、後編にもう少し詳しく書けたらいいなぁ...
* 詰まったところで、すぐ @hasumikin さんや @takkanm さんに相談すれば、
  「もっと早く解決することはできた」と思うけれど、性格的に
  自分で色々試して納得しながら進めていきたいので(それが例え、 :turtle: の歩みであっても)、
  これからもマイペースにやっていこうと思います。


明日は、 @takkanm さんです。

キーボード、いくつ持ってるんだろう。
