---
layout: post
title:  "Rails Girls Guides「Carrierwave を使ってサムネイルを作ってみよう」のPRを送った話"
date:   2017-12-02 00:19:00 +0900
tags: [railsgirls]
---
この記事は、<a href="https://qiita.com/advent-calendar/2017/railsgirlsjapan" target="_blank">Rails Girls Japan Advent Calendar 2017 </a>の１日目の記事です。

私が言い出しっぺで、昨年から始めたRails Girls Japanのアドベントカレンダーですが、  
空いている日をもっと埋められないかな～と、今朝思っていたら、閃いてしまったのです。

<blockquote class="twitter-tweet" data-lang="ja">
<div dir="ltr" lang="ja">
Rails Girls Japanのアドベントカレンダー <br />
ガイドの古いバージョンのままになってるところを、修正してやったこと載せていけば、カレンダー埋まるし、どういう風にやったらいいかも共有できるから、一石二鳥じゃない？って閃いてしまった✨</div>
— emorima (@emorima) <a href="https://twitter.com/emorima/status/936390046935945217?ref_src=twsrc%5Etfw">2017年12月1日</a></blockquote>
<script async="" charset="utf-8" src="https://platform.twitter.com/widgets.js"></script><br />

このRails Girls Guies(日本語訳) は、[Rails Girls 本家のGuide](http://guides.railsgirls.com/)の日本語訳になっています。  
更新は、Rails Girls ワークショップの開催前に、コーチの方達が  
1～4(インストールからアプリの公開まで)を一通り確認した時に、  
Ruby や Railsのバージョンが古くなっていたりしたときに、主に更新されていますが、  
本家の情報の更新に合わせても[有志](https://github.com/railsgirls-jp/railsgirls-jp.github.io/graphs/contributors)によって、更新されています。

リポジトリは公開されているので、古い情報を見つけたら、  
気軽にプルリクエストを送って欲しいので、いつも私がやっている修正する方法を記載します。

では、いってみましょう。

### (初めての方)GitHubからソースを取得する

【前提】既にGitHubアカウントを持っているものとします。

[Rails Girls Guides](https://github.com/railsgirls-jp/railsgirls-jp.github.io)のリポジトリをブラウザで開きます。

画面右上にある Fork ボタンをクリックします。  

<div class="separator" style="clear: both; text-align: center;">
<a href="https://1.bp.blogspot.com/-ExhY85LZaU8/WiFfcDw6srI/AAAAAAAAK1o/2bjYSTdmisgQXgjpw9ECXhRDSJWm8oPmwCLcBGAs/s1600/FireShot%2BCapture%2B168%2B-%2Brailsgirls-jp_rai_%2B-%2Bhttps___github.com_railsgirls-jp_railsgirls-jp.github.io.png" imageanchor="1" style="clear: left; float: left; margin-bottom: 1em; margin-right: 1em;">
  <img border="0" data-original-height="54" data-original-width="372" height="46" src="https://1.bp.blogspot.com/-ExhY85LZaU8/WiFfcDw6srI/AAAAAAAAK1o/2bjYSTdmisgQXgjpw9ECXhRDSJWm8oPmwCLcBGAs/s320/FireShot%2BCapture%2B168%2B-%2Brailsgirls-jp_rai_%2B-%2Bhttps___github.com_railsgirls-jp_railsgirls-jp.github.io.png" width="320" />
  </a>
</div>

すると、自分のアカウントにRails Girls Guidesのリポジトリがコピーされます。

ターミナルを開いて、clone します。

```sh
$git clone git@github.com:[アカウント名]/railsgirls-jp.github.io.git

# ssh の設定をしていない人は、https で取得
$git clone https://github.com/[アカウント名]/railsgirls-jp.github.io.git
```

ガイドの修正を始める前に、branchを作成して、そこで作業しましょう。

```sh
$git checkout -b update_thumbnails
```
Rails Girls Guidesは、Railsではなく、jekyll という静的なサイトを作るのに便利なRubyで作ったツールです。

以下の方法で、起動できます。

```sh
$cd railsgirls-jp.github.io
$bundle
$bundle exec jekyll serve --watch [--host 0.0.0.0]
```

ブラウザで、http://127.0.0.1:4000/ を開くと、Rails Girls Guides(日本語版) のトップ画面が表示されます。

さて、これで準備できました。

では、修正していきましょう。

修正したいファイルを見つけます。

jekyll では、_posts フォルダ内のmarkdown形式のファイルがあり、このファイルが画面に表示される元ファイルになります。

jekyll を起動して、ブラウザで表示すると、markdown形式のファイルを、_sites 以下にhtmlファイルに変換したものが表示されています。

(_sites 以下のファイルを修正しても、markdownを修正しないと jekyll を再起動すると修正は消されてしまうので、注意が必要です)

今日は、「Carrierwave を使ってサムネイルを作ってみよう」で使っている gemのバージョンを新しくしようと思います。

Rails Girls では、できるだけ最新のRuby, Rails のバージョンを使っているので、  
修正するガイドもそのバージョンに合わせて、動作確認を行ってください。  
(12/1現在、 Ruby 2.4.2, Rails 5.1.4のバージョンの環境となっています。)  

主に、以下の点の確認をします。

- リンク先が切れていないか
- リンク先は正しいか (本家サイトと比べてみたりする) 
- 使っているgemのバージョンは最新か
- 記述してある内容通りに実行して正常に動くか

今回は、ダウンロードのリンクの修正と、gem のバージョンを修正しました。

```sh
$git add _posts/2012-06-03-thumbnails.markdown
$git commit -m 'update thumbnails section'
$git push origin update_thumbnails
```

これで、GitHub上のサイトに、修正したブランチが作成できます。  
GitHubの画面からPull Requestを作成して、終了です！

後は、レビューされてマージされるか、コメントもらうのを待ちましょう！

<a href="https://github.com/railsgirls/railsgirls.github.io/pull/361">本家のpull request</a>も作成しました。
