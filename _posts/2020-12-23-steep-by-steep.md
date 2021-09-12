---
layout: post
title:  "Steep by Steep(Day2)"
date:   2020-12-23 23:00:00 +0900
tags: [ruby]
---
この記事は、<a href="https://qiita.com/advent-calendar/2020/kcscarrot">ケーシーエスキャロット Advent Calendar 2020</a>&nbsp;23日目の記事です。


昨日は、naokishi の&nbsp;<a href="https://qiita.com/naokishi/items/f9d2f53bb103241b2817" target="_blank">aws cli と jq コマンド</a>&nbsp;でした。  
今、AWS使ってるんですねー。もうWindowsアプリは開発していないのかな…？  

さて、<a href="{{relative_url}}/2020/12/18/steep-by-steep">前回</a>はエラーが増えたところで終わりました。

一度、<a href="https://github.com/soutaro/steep" target="_blank">リポジトリ</a>&nbsp;にあるサンプルコードを実行してみようか…ということで、コピペで実行してみると…

```sh
$ bundle exec steep check --log-level=fatal
lib/phone.rb:9:2: MethodBodyTypeMismatch: method===, expected=bool, actual=(bool | nil) (def ==(other))
  (bool | nil) <: bool="" false="" nil="" true="="> nil 
<: bool="" false="" nil="" true="=">
```

エラーが出ました... が、一昨日出ていたエラーとは違うし、  
このメソッド以外のチェックは通っているようなので、  
このエラーは置いておいて、メソッドの定義について確認してみます。

うーん…(コードを見比べている)

！！！

メソッドのパラメータだ！！！！

で、以下のようにコードを修正して実行してみると…

```ruby
class User
  def initialize(no:, name:)                                                                                                
    @no = no
    @name = name
    @scores = Array.new
  end
  attr_reader :no, :name, :scores

  def add(score:)
    @scores << score
  end
end
```

```sh
$ bundle exec steep check --log-level=fatal
bin/main.rb:20:2: NoMethodError: type=singleton(::File), method=open (File.open(fpath, "w") {|fd| fd.write data })
bin/main.rb:22:24: ArgumentTypeMismatch: receiver=singleton(::Analyzer), expected={ :path => untyped }, actual=::String (fpath)
lib/analyzer.rb:6:2: MethodArityMismatch: method=(self) (def self.load(path))
lib/analyzer.rb:15:2: MethodArityMismatch: method=(self) (def self.parse_row(row))
lib/analyzer.rb:16:8: IncompatibleArguments: receiver=singleton(::User), method_type=(name: untyped, no: untyped) -> ::User (User.new(row["No"], row["Name"]))
```

メソッドの定義がSteepで解析されて、Userのエラーが消えました!!!

同じように、Analyzerも修正すると、

```sh
bin/main.rb:20:2: NoMethodError: type=singleton(::File), method=open (File.open(fpath, "w") {|fd| fd.write data })
bin/main.rb:22:24: ArgumentTypeMismatch: receiver=singleton(::Analyzer), expected={ :path => untyped }, actual=::String (fpath)
lib/analyzer.rb:16:8: IncompatibleArguments: receiver=singleton(::User), method_type=(name: untyped, no: untyped) -> ::User (User.new(row["No"], row["Name"]))
```

エラーが更に減った〜

出ているエラーが全部違うので、ここからは１つずつ確認していこう…

### NoMethodError

これはRubyを使っているとよく見るエラーですが、Rubyの<a href="https://docs.ruby-lang.org/ja/latest/class/NoMethodError.html" target="_blank">NoMethodError</a>ではなく、<a href="https://github.com/soutaro/steep/blob/v0.38.0/lib/steep/type_inference/method_call.rb#L95" target="_blank">TypeInference::MethodCall::NoMethodError</a>&nbsp;です。

エラーとして検出されている場所は、`File.open()` のRubyのクラス。  
Rubyのクラス検知して、エラーにされている???

うーん...&nbsp; steep の `--verbose` オプション付けて、ログ出力してみるか…

```sh
$ bundle exec steep check --log-level=fatal --verbose --log-output="./check.log"
```

出力されたログを確認してみると、、、  
あぁ、なるほど、<a href="https://github.com/ruby/rbs/tree/v0.20.1/core" target="_blank">ここ</a>のファイル読み込んでる。  
で、File.rbs を見てみると…

なるほど、確かに `File.open()` が定義されていない…。  
initialize が定義されているので、 `File.new()` に変えてみて、実行してみる。

```sh
bin/main.rb:22:2: UnexpectedBlockGiven: method_type=((::string | ::_ToPath | ::int), ?(::string | ::int), ?::int) -> ::File (File.new(fpath, "w") {|fd| fd.write data })
bin/main.rb:25:24: ArgumentTypeMismatch: receiver=singleton(::Analyzer), expected={ :path => untyped }, actual=::String (fpath)
lib/analyzer.rb:16:8: IncompatibleArguments: receiver=singleton(::User), method_type=(name: untyped, no: untyped) -> ::User (User.new(row["No"], row["Name"]))
```

お、エラーが変わった。(行数が変わっているのは、元のコードをコメントアウトして残しているため)

UnexpectedBlockGiven は、`File.new()` の定義は、Fileクラスを返す定義になっていて、blockを渡す定義がされていないからだな。なるほど、なるほど。

`File.open()` の行を以下のように結局書き換える。

```ruby
 # rbs に File.open()が定義されていない
 # File.open(fpath, "w") {|fd| fd.write data }
 fd = File.new(fpath, "w")
 fd.write data
 fd.close
```
  
```sh
$ bundle exec steep check --log-level=fatal
bin/main.rb:26:24: ArgumentTypeMismatch: receiver=singleton(::Analyzer), expected={ :path => untyped }, actual=::String (fpath)
lib/analyzer.rb:16:8: IncompatibleArguments: receiver=singleton(::User), method_type=(name: untyped, no: untyped) -> ::User (User.new(row["No"], row["Name"]))
```

やった！ main.rb で出ていた、Fileクラスのエラーが消えました！

### ArgumentTypeMismatch, IncompatibleArgument

この２つのエラーですが、キーワード引数に変更した時の呼び出し側の修正をしていなかったことで出ていたエラーでした。

bin/main.rb 実行したらエラーになったので、修正したら、上記エラーはでなくなりました。(テスト書いてないから、こういうことになるんだよ…テスト大事)

エラーが出なくなったから、終わり！！！にはなりません。

なぜなら、rbsファイルには、`untyped` の宣言しかしていないのだから…

本番はこれからです。

## rbsファイルに型を定義する

rbs ファイルを以下のように修正しました。  
(Structを使ったクラスはとりあえず、untypedのまま)

```ruby
class User
  @no: Integer
  @name: String
  @scores: Array[untyped]
  def initialize: (no: Integer, name: String) -> untyped
  def add: (score: untyped) -> untyped                                                                                      
end

class Analyzer
  def self.load: (path: String) -> Array[User]
  def self.parse_row: (row: CSV::Row) -> User
end
```

これで実行すると、、、

```sh
$ bundle exec steep check --log-level=fatal
sig/sample.rbs:11:28...11:36	UnknownTypeNameError: name=::CSV::Row
```

CSVクラスは、標準添付ライブラリのため、requireして使っているので、Steepfile のlibrary で追加します。

```ruby
target :app do
  signature "sig"

  check "bin"
  check "lib"

  library "csv"                                                                                                             
end
```
そして実行すると、、、

```sh
$ bundle exec steep check --log-level=fatal
lib/analyzer.rb:9:30: IncompatibleAssignment: lhs_type=::CSV::Row, rhs_type=::Array[(::String | nil)] (row)
  ::Array[(::String | nil)] <: ::basicobject="" ::csv::row="=" ::object=""> ::BasicObject <: ::csv::row="" code="" does="" hold="" not="">
```

やっとそれっぽいエラーが出てきました。

エラーメッセージに、lhs_type, rhs_typeが出ていますが、lhs_type がrbsで定義している型、rhs_typeが想定している型が表示されています。

CSV.foreach() のブロック引数のrow は、実行すると、CSV::Rowのインスタンスとなり、parse_row()のパラメータに設定されるのですが、<a href="https://github.com/ruby/rbs/blob/v0.20.1/stdlib/csv/0/csv.rbs#L177" target="_blank">rbsのCSV.foreach()</a>&nbsp;では、Array[String?] で定義されているようです。

rbsに合わせて、Analyzer.parse_row() の引数をArray[String?] とすると、今度はrow.headers でエラーになってしまう…

悩ましい…

そこで、CSVは、rbsを参考に使用しているメソッドの以下の定義を sig/sample.rbsに追加することにしました。(Steepfileのlibrary定義はコメントアウトしました)

```ruby
class CSV < Object
  def self.foreach: [U] (String, ?::Hash[Symbol, U] options) { (CSV::Row arg0) -> void } -> void
end

class CSV::Row < Object
  alias [] field                                                                                                                                                               
end
```

実行してみると、、、  
エラーがなくなり、Steepでの型解析は正常となったようです。

## 使ってみての感想

Rubyのクラスについては、rbsの定義を参照しているので、rbsに定義されていない型だとエラーになってしまうようです。  
そのため、普段書き慣れている記述でエラーになるケースもあり、この小さなプログラムでも確認したりするのに、結構時間を使いました。

使ってみての感想は、「やっぱり型書くのは大変だな…」という印象が強いのですが、  
IDEを使ってコードを書く時に型情報までSuggest表示されれば、  
Rubyの経験があまりない人でも書きやすかったり、大人数のプロジェクトなどでは、プログラムの記述が揃うし、確かにバグは少なくなるような気がします。

でも、やっぱりRubyは自由に書きたい…。
