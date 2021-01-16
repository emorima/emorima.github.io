---
layout: post
title:  "Rails Girls Guides「Test your app with RSpec」を本家に合わせてみる"
date:   2017-12-02 20:03:00 +0900
tags: [railsgirls]
---
この記事は、<a href="https://qiita.com/advent-calendar/2017/railsgirlsjapan" target="_blank">Rails Girls Japan Advent Calendar 2017 </a>の２日目の記事です。

今日は、「Test your app with RSpec」の章が、昔の記法のRspecのままで古いので、  
<a href="http://guides.railsgirls.com/testing-rspec" target="_blank">本家</a>に合わせてみましょう。

こちらは、日本語訳されていません。  
翻訳に参加する場合は、issueを作成するとREADME.md に書いてあるので、<a href="https://github.com/railsgirls-jp/railsgirls-jp.github.io/issues/322" target="_blank">issue</a> を作っておき、今度翻訳することにします。

さて、本家の内容と合わせるために、<a href="https://github.com/railsgirls/railsgirls.github.io">本家のリポジトリ</a>を確認します。

本家のサイトはpull request を作成する予定がいまのところないのであれば、直接cloneしてしまいましょう。


```sh
$git clone git@github.com:railsgirls/railsgirls.github.io.git
```

diff コマンドなどで、対象のファイルの差分を一応確認してみます。<br />

```sh
$cd railsgirls-jp.github.io # Rails Girls Guides(日本語訳)のディレクトリに移動
$git checkout -b sync_testing # 作業するためにブランチを作ります
$diff -u _posts/2014-01-30-testing.markdown (本家サイトのレポジトリをcloneした場所)railsgirls.github.io.git/_posts/2014-01-30-testing.markdown
```

たくさん修正されています。<br />
この&nbsp;_posts/2014-01-30-testing.markdown はそのまま本家のファイルをコピーすればよさそうなので、  
本家のファイルをコピーして、pull requestを出しましょう。

```sh
$cp (本家サイトのレポジトリをcloneした場所)railsgirls.github.io.git/_posts/2014-01-30-testing.markdown _posts/2014-01-30-testing.markdown
$git add _posts/2014-01-30-testing.markdown
$git commit -m '本家サイトの内容で更新'
$git push origin sync_testing
```

昨日と同じように、GitHubの画面で pull request を作成して終了です!

<a href="https://github.com/railsgirls-jp/railsgirls-jp.github.io/pull/323">今日のpull request</a>
