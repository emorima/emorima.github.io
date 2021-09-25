---
layout: post
title:  "prk_firmware 環境構築(Linux) その1"
date:   2021-09-25 12:55:00 +0900
tags: [mruby, prk_firmware, keyboard, linux]
---

RubyKaigi Takeout 2021 の hasumikinさんの発表で
実質Rubynのキーボードが作れるということなので、
meishi2 で試したことをメモ。

## これまでのキーボード

自作キーボードは既に作ったことがあって(<a href="{{relative_url}}/2018/12/19/mechanical-keyboard-debut">自作キーボードデビューの話</a>)、
<a href="https://eucalyn.hatenadiary.jp/entry/about-mint60-01">Mint60</a>以外にも、
<a href="https://booth.pm/ja/items/1076720">Zinc</a>、
<a href="https://shop.yushakobo.jp/products/ergodash">ErgoDash</a> を作ったりした。

<a href="https://github.com/qmk/qmk_firmware">qmk_firmware</a>の環境も自分のPC(Linux)の構築済み。

## 実質Rubyのキーボードを作るために買ったもの

道具は一通り揃っているので、作るキーボードと実質Rubyのキーボードに対応しているPro Micoを購入した

* [meishi2@遊舎工房](https://shop.yushakobo.jp/products/meishi2)
* [Pro Micro RP2040【DEV-17717】@マルツオンライン](https://www.marutsu.co.jp/pc/i/40719713/)

## 環境構築(Linux)

meishi2は、[ビルドガイド](https://biacco42.hatenablog.com/entry/2019/08/10/185624)を参考にして、はんだ付け。

(はんだ付けしながら、もう老眼鏡かけないと見えない現実と向き合う。)


はんだ付けができたら、いよいよ Pro Microの書き込みをしていく。

[ここ](https://github.com/picoruby/prk_firmware#building-a-binary-by-yourself)を見ながら進める。


CRuby(MRI)のインストールはしてあるので、OK。

### Raspberry Pi Pico C/C++ SDKのSetupを行う


[ここ](https://github.com/raspberrypi/pico-sdk#quick-start-your-own-project)を見ながら進める。

cmake とか必要なあたりをinstall

```
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib
```

次は、Raspberry Pi Pico SDK の環境を、prk_firmwareに合わせて作る。

いくつか方法があるようで、その方法で、`CMakeLists.txt`の書き方が変わるようなのだけど、
[prk_firmwareのCMakeLists.txt](https://github.com/picoruby/prk_firmware/blob/master/CMakeLists.txt) 見ると、
git cloneしているやり方のようなので、その手順で進める。

```
$ cd ~/repo/picoruby/  # 作業ディレクトリ
$ git clone git@github.com:raspberrypi/pico-sdk.git
```

`pico_sdk_import.cmake` を自分のプロジェクトのディレクトリにコピーすると書いてあるけど、
prk_firmwareのリポジトリに、`pico_sdk_import.cmake` があって、差分はほどんとない(空行追加のみ)ので、
コピーする必要はなさそう。

PICO_SDK_PATH のパスを通しておく。

```sh
export PICO_SDK_PATH=/home/emorima/repo/picoruby/pico-sdk/
```

### prk_firmwareのSetupを行う

qmk_firmware は、1つのリポジトリで全てのキーボードのキーマップを管理していて、
[qmk_firmwareのリポジトリ](https://github.com/qmk/qmk_firmware)で、
管理されている全てのキーボードのkeymapsが入っているけれど(HHKBまで!)、
prk_firmwareは、キーボードのキーマップは別のリポジトリ(各自)で作成してるとのこと。


prk_firmware のリポジトリをcloneして、今回作成するキーボードのmeishi2のリポジトリもcloneしてくる。


```sh
cd ~/repo/picoruby/ # 作業ディレクトリ
$ git clone --recursive git@github.com:picoruby/prk_firmware.git
$ cd prk_firmware/keyboards
$ git clone git@github.com:picoruby/prk_meishi2.git
$ cd prk_meishi2/build
```

必要に応じて、 `prk_meishi2/keymap.rb` を編集する。
(このkeymap.rbがRubyファイルなので、Rubyでメソッド定義ができる！)

いよいよmakeしていく。

```
$ cmake -DPRK_NO_MSC=1 ../../..
Using PICO_SDK_PATH from environment ('/home/emorima/repo/picoruby/pico-sdk/')
PICO_SDK_PATH is /home/emorima/repo/picoruby/pico-sdk
Defaulting PICO_PLATFORM to rp2040 since not specified.
Defaulting PICO platform compiler to pico_arm_gcc since not specified.
-- Defaulting build type to 'Release' since not specified.
PICO compiler is pico_arm_gcc
-- The C compiler identification is GNU 9.2.1
-- The CXX compiler identification is GNU 9.2.1
-- The ASM compiler identification is GNU
-- Found assembler: /usr/bin/arm-none-eabi-gcc
Defaulting PICO target board to pico since not specified.
Using board configuration from /home/emorima/repo/picoruby/pico-sdk/src/boards/include/boards/pico.h
-- Found Python3: /usr/bin/python3.9 (found version "3.9.5") found components: Interpreter 
CMake Warning at /home/emorima/repo/picoruby/pico-sdk/src/rp2_common/tinyusb/CMakeLists.txt:10 (message):
  TinyUSB submodule has not been initialized; USB support will be unavailable

  hint: try 'git submodule update --init' from your SDK directory
  (/home/emorima/repo/picoruby/pico-sdk).


-- Configuring done
CMake Error at lib/CMakeLists.txt:15 (add_library):
  Cannot find source file:

    ./picoruby/src/mrubyc/src/mrblib.c

  Tried extensions .c .C .c++ .cc .cpp .cxx .cu .m .M .mm .h .hh .h++ .hm
  .hpp .hxx .in .txx


-- Generating done
CMake Generate step failed.  Build files cannot be regenerated correctly.
```

pico-sdk のsubmoduleがなくて、エラーなっているようなので、hintの指示に素直に従う。

```
$ cd ~/repo/picoruby/pico-sdk/
$ git submodule update --init
$ cd -
```

もう1回チャレンジ

```
$ cmake -DPRK_NO_MSC=1 ../../..
PICO_SDK_PATH is /home/emorima/repo/picoruby/pico-sdk
PICO platform is rp2040.
PICO target board is pico.
Using board configuration from /home/emorima/repo/picoruby/pico-sdk/src/boards/include/boards/pico.h
TinyUSB available at /home/emorima/repo/picoruby/pico-sdk/lib/tinyusb/src/portable/raspberrypi/rp2040; adding USB support.
-- Configuring done
CMake Error at lib/CMakeLists.txt:15 (add_library):
  Cannot find source file:

    ./picoruby/src/mrubyc/src/mrblib.c

  Tried extensions .c .C .c++ .cc .cpp .cxx .cu .m .M .mm .h .hh .h++ .hm
  .hpp .hxx .in .txx


-- Generating done
CMake Generate step failed.  Build files cannot be regenerated correctly.
```

今度は、`./picoruby/src/mrubyc/src/mrblib.c` が見つからないよと言われてしまった。
GitHubで、prk_frmwareのリポジトリからみていくと、確かにないなぁ。

何か手順漏れてたかな。。。と見返したりして、ひらめいた。
prk_firmware の lib/picoruby が picoruby/picorubyを参照しているんだけど、もしや...
picoruby からの参照先にはないけど、mruby/c のmasterブランチには入ってる。これっぽい！

これは、[@hasumikin](https://twitter.com/hasumikin/)に相談した方がよさそうなので、続きは夜！

