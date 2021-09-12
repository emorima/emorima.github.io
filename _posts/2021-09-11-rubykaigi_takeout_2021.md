---
layout: post
title: "RubyKaigi Takeout 2021に参加した"
date:   2021-09-11 22:00:00 +0900
tags: [ruby, rubykaigi]
---
2021/09/09(Thu.)-11(Sat.) [RubyKaigi Takeout 2021](https://rubykaigi.org/2021-takeout) に参加した。

Day1, Day2 は作業や打ち合わせやらで脇見しながらだったので、あまりしっかり見れなかった。後で配信動画ちゃんと見よう。
Day3は、朝からRails Girls More!のDiscordでみんなで集まって、見ていた。

動画配信サイト、Asakusa.rbでテスト視聴させてもらって、その時もリアルで修正していたけど、
会議中もどんどん改善されていて、すごかった。(@sora_h すごい!)

運営のみなさん、楽しい会議ありがとうございました！

### [@mame](https://twitter.com/mametter) さんのキーノート

「時代はvscodeなのか。。。」というメモ書きを残していた。

すごい便利そうな機能なので使ってみたい気持ちはある。けど、RBS書くの大変だし、IDE...

昔々、C++ Builderを使って開発していた時に、自分の入力より、補完のリスト出るのが遅くて(メモリの問題もある)、
邪魔になってOFFにしていたからなぁ。。。今はメモリも増えてるから、補完も快適に使えるようになっているのかな。

あと、「mameさんの書く超絶技巧プログラミングは型推論できないよね…」と思って見ていた。


### [@hasumikin](https://twitter.com/hasumikin) のキーボードは実質Ruby

キーボード熱が高まった。

キー入力で処理するプログラムがRubyで書けるなんて、全レイヤーのキー配列に処理追加したくなっちゃう。
早速Pico Micro買った。

あとは、[PicoRuby](https://github.com/picoruby/)あたりのリポジトリ覗いて、keymaps.rbにどんな設定が必要なのかみておこう。

[Fukuoka.rb#226](https://fukuokarb.connpass.com/event/224589/) で、Ruby-modeの話をもうチョット詳しく説明してくれるようなので、申し込んだ。


### [@shugomaeda](https://twitter.com/shugomaeda) のrefinementsのお話

仕様の部分がわかりづらい?ことで、バグ報告としてあがってくるから、変更するというお話だった。

RubyのAPI実装については、[@akr](https://twitter.com/tanaka_akr)さんの 「[APIデザインケーススタディ-Rubyの実例から学ぶ。問題に即したデザインと普遍の考え方-](https://www.amazon.co.jp/dp/B07JWJ513B/)」でも、
ユーザ(Rubyを使う人)を考えるとこういうAPIにした方がよいという内容があったので、
こうやってcommiterの人たちは、Rubyをよくしていこうとしているんだなぁ。。。と思った。

あと、最後の釣り動画、見たかった。

### [@aycabta](https://twitter.com/aycabta)の~~沢登り動画~~Graphical Terminal User Interface of Ruby 3.1

ごめんなさい、動画のインパクト強すぎて、内容入ってないので、後で見る。


### [@mrkn](https://twitter.com/mrkn)さんのCharty

仕事でグラフを表示することも多くて、gnuplotも使ったりしていたけど、Chartyほんと使いやすそうだし、軽そう。

svgで出力されるなら、そのまま表示もできそうだし、実際使えるか試してみようかな。

### [@ima1zumi](https://twitter.com/ima1zumi) Dive into Encoding

昔々、OS(Linux) EUC、データベース EUC, WebサイトはSJISとか普通にあったから、
Encodingすごい大変だったんだけど、 (メールもUTF-8で表示できるようになってるし、)
あまり最近Encodingで詰まることなくなったなぁ。。。と思って見ていた。

Encodingに興味をもって、色々調べたんだろうな。。。と思う発表でした！

新しいEncoding追加する定義が、キーマップに見えてしまった。。。

### [@hsbt](https://twitter.com/hsbt) How to develop the Standard Libraries of Ruby?

ライブラリをRuby本体から切り離して、gem化の作業。

test書き換えとかもやっていて、柴田さん、本当にすごいな。

### [@matschaffer](https://twitter.com/matschaffer) 10 years of Ruby-powered citizen science

"地震" と聞いて...

仕事で、Google Mapに情報表示したりすることもあるので、サイトの表示速度とかどうやって早くしてるんだろ？と、
途中気になってしまって、色々調べ始めたりしていた。


### エモリハウスパーティー

Day2 18:00~ SpatialChat でエモリハウスパーティーをした。

と言っても、SpatialChatのスペースを作っただけで、他はなにもしてないのだけど。

ある年のAsakusa.rb新年会の構図にならないか、ちょっと心配だったんだけど、
たくさんの人が、ふらっと来てくれて嬉しかった！

RubyKaigi の懇親会って、いろんな人にふらっと出会える良さがあると思っていて、
その感じがでていたら嬉しいなぁ。
