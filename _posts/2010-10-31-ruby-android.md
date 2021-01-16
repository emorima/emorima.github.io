---
layout: post
title:  "RubyでAndroid　(準備編：その１）"
date:   2010-10-31 20:57:00 +0900
tags: [ruby]
---
RubyでAndroidアプリを開発したいと思ったので、

ubuntu10.10 に、開発環境を作りたいと思います。

今回はその準備編（その１）です。

##### Android SDKをインストールする。

[このページ](http://developer.android.com/sdk/index.html)にあるSDKを取得し、解凍する。

```sh
$wget http://dl.google.com/android/android-sdk_r07-linux_x86.tgz

$tar -xvf android-sdk_r07-linux_x86.tgz
```

解凍されたディレクトリ内を見ると、 "SDK Readme.txt"(ファイル名にスペースあり!)があるので、 開いてみる。

```text
Welcome to the Android SDK!

(snip)

To start the SDK Manager, please execute the program "tools/android".
(snip)
```

書いてある通りに、実行すると、以下のウィンドウが起動。

<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/_LmadTI02_zs/TMmeGSqScMI/AAAAAAAAAHg/A4TVARyP6T8/s1600/Screenshot.png" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" height="240" src="http://3.bp.blogspot.com/_LmadTI02_zs/TMmeGSqScMI/AAAAAAAAAHg/A4TVARyP6T8/s320/Screenshot.png" width="320" /></a></div><br />

##### rhodesをインストールする。

```sh
$gem install rhodes
[sudo] password for (user):
(パスワード入力)
Building native extensions.  This could take a while...
Successfully installled highline-1.6.1
Successfully installed diff-lcs-1.1.2
Successfully installed extlib-0.9.15
Successfully installed templater-1.0.0
Successfully installed activesupport-2.3.10
Successfully installed rhodes-2.2.2
6 gems installed
(snip)

```

##### JDKをインストールする。

[このページ](http://java.sun.com/javase/ja/6/download.html)からにあるJDKを取得する。

（この時点では、JDK 6 Update 21 を取得しました。）

```sh
$wget http://cds.sun.com/is-bin/(長いURL)/jdk-6u21-linux-i586.bin
$chmod +x jdk-6u21-linux-i586.bin
$./jdk-6u21-linux-i586.bin
```

##### Android NDKをインストールする。

[このページ](http://d.android.com/sdk/ndk/index.html)からにあるAndroid NDKを取得する。

```sh
$wget http://dl.google.com/android/ndk/android-ndk-r4b-linux-x86.zip
$unzip android-ndk-r4b-linux-x86.zip
```

##### rhodesをセットアップする。

```sh
$rhodes-setup
We will ask you a few questions below about your dev environment.

JDK path (required) () : (JKDをインストールしたパスを入力)
Android SDK path (blank to skip) (): (Android SDKをインストールしたパスを入力)
Android NDK path (blank to skip) (): (Android NDKをインストールしたパスを入力)
Windows Mobile 6 SDK Cabwiz (blank to skip) (): (SKIPする)
BlackBerry JDE 4.6 (blank to skip) (): (SKIPする)
BlackBerry JDE 4.6 MDS (blank to skip) (): (SKIPする)
BlackBerry JDE 4.2 (blank to skip) (): (SKIPする)
BlackBerry JDE 4.2 MDS (blank to skip) (): (SKIPする)

If you want to build with other BlackBerry SDK version edit: (rhodesインストールパス)/rhobuild.yml
```

これで、一通りのインストールは完了(のはず)。


##### アプリのプロジェクトを新規作成する。

```sh
$rhogen app sampleroid http://localhost:9292/application
Generating with app generator:
    [ADDED]  sampleandroid/rhoconfig.txt
    [ADDED]  sampleandroid/build.yml
    [ADDED]  sampleandroid/app/application.rb
    [ADDED]  sampleandroid/index.erb
    [ADDED]  sampleandroid/inex.bb.erb
    [ADDED]  sampleandroid/layout.erb
    [ADDED]  sampleandroid/loading.html
    [ADDED]  sampleandroid/Rakefile
    [ADDED]  sampleandroid/app/helpers
    [ADDED]  sampleandroid/icon
    [ADDED]  sampleandroid/app/Settings
    [ADDED]  sampleandroid/public
```

あと一息…。

##### 起動。

```sh
$rake run:android
(in (プロジェクトパス))
cd (rhodesインストールパス)


Your java bin folder does not appear to be on your path.
This is required to use rhodes.

+++ No required platform (API level &gt;= 4) found, can't proceed.
```

エラーになりました。(涙)   続きは その２で。

<div style="background-color: tomato;">参考URL</div>

- [たかはまんの三日坊主日記NEXT](http://10nen.ossclub.net/tiovitan2000/2010/09/02/android-iphone%E7%AD%89%E3%81%AE%E3%82%A2%E3%83%97%E3%83%AA%E3%82%92rhodes%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6ruby%E3%81%A7%E9%96%8B%E7%99%BA/)
