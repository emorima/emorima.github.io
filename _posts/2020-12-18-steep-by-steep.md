---
layout: post
title:  "Steep by Steep"
date:   2020-12-18 00:58:00 +0900
categories: [ruby]
tag: []
---
この記事は、<a href="https://qiita.com/advent-calendar/2020/kcscarrot">ケーシーエスキャロット Advent Calendar 2020</a>&nbsp;17 日目の記事です。(ちょっと過ぎちゃったけど)

Ruby 3の静的解析機能の１つ。steep を使ってみようと思います。

リポジトリは、<a href="https://github.com/soutaro/steep">こちら</a>

タイトルの Steep by Steep は、中学の頃流行った New Kids On The Block の "Step By Step" にかけてみました。

<iframe allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" frameborder="0" height="315" src="https://www.youtube.com/embed/ay6GjmiJTPM" width="560"></iframe>&nbsp;

#### インストール

Rubyは、3.0.0-preview2を使っています。  
steepのinstallをします。gem でinstallします。

```sh
$ gem install steep
Successfully installed steep-0.38.0
Parsing documentation for steep-0.38.0
Installing ri documentation for steep-0.38.0
Done installing documentation for steep after 2 seconds
1 gem installed
```

サンプルで使うコードです。

色々な型を試してみたかったので、設計面は無視したコードになっています。


#### lib/user.rb

```ruby
class User                                                                                                                                                                             
  def initialize(no, name)
    @no = no
    @name = name
    @scores = Array.new
  end
  attr_reader :no, :name, :scores

  def add(score)
    @scores << score
  end
end
```

#### lib/analyzer.rb

```ruby
require 'csv'                                                                                                                                                                          
  
class Analyzer
  @@score = Struct.new("Score", :subject, :score)

  def self.load(path)
    users = Array.new
    CSV.foreach(path, headers: true) do |row|
      users << parse_row(row)
    end

    users
  end

  def self.parse_row(row)
    u = User.new(row["No"], row["Name"])

    row.headers[2..-1].each do |subject|
      u.add @@score.new(subject.downcase, row[subject].to_i)
    end

    u
  end
end
```

#### bin/main.rb

```ruby
LOAD_PATH &lt;&lt; File.join(File.dirname(File.expand_path(__FILE__)), "../lib")                                                                                                            
  
require 'user'
require 'analyzer'

# 使用するデータ
data = <<-EOS
No,Name,English,History,Science
1,Akina,85,88,87
2,Bob,78,95,85
3,Candy,83,80,92
4,David,85,83,88
5,Emily,78,93,85
6,Fumiya,80,82,89
7,George,92,88,79 
EOS
```

それでは、実際に使ってみます

```sh
$ steep init
```

と入力すると、Steepfile が作成されるので、ここに定義をしていきます。

#### Steepfile

```ruby
target :app do
  signature "sig"

  check "bin"
  check "lib"
end
```

これだけ定義して、まず実行してみます。


```sh
$ bundle exec steep check --log-level=fatal
bin/main.rb:20:2: NoMethodError: type=singleton(::File), method=open (File.open(fpath, "w") { |fd| fd.write data })
lib/user.rb:7:2: NoMethodError: type=self, method=attr_reader (attr_reader :no, :name, :scores)
```

エラーが出ました。  
main.rbはおいておいて、Userクラスで、attr_reader に定義をしていないことを指摘しているようなので、定義を追加してみます。

定義ファイルは、sigフォルダの中に `.rbs`ファイルで書くとあるので、  
sig/sample.rbs を作成して以下のように定義してみました。

```ruby
class User                                                                                                                                                     
  @no: Integer
  @name: String
  @scores: Array[Score]
end
```

もう１度実行してみると、

```sh
sig/user.rbs:4:17...4:22        UnknownTypeNameError: name=::Score
```

エラーが変わりました。  
ScoreというTypeがわからないというエラーが出ました。

これは、Analyzerクラスの中でStructで定義しているからかな…

ひとまず、先に進めるために `untyped` として、もう１回実行してみると、次のエラーに変わりました。

```sh
lib/user.rb:2:2: MethodArityMismatch: method=initialize (def initialize(no, name))
```

initializeの定義をしていないので、次はメソッドの定義をしていきます。

こんな感じかな(とりあえずuntyped)

```ruby
class User
  @no: Integer
  @name: String
  @scores: Array[untyped]
  def initialize: (no: untyped, name: untyped) -&gt; untyped                                                           
  def add: (score: untyped) -&gt; untyped
end
```

実行してみると、、、

```sh
lib/user.rb:2:2: MethodArityMismatch: method=initialize (def initialize(no, name))
lib/user.rb:9:2: MethodArityMismatch: method=add (def add(score))
```

エラーになってしまいました…  
Analyerクラスが定義されていないことによる影響かな…と頭をよぎったので、Analayzerクラスも定義してみます。

```ruby
class User
  @no: Integer
  @name: String
  @scores: Array[untyped]
  def initialize: (no: untyped, name: untyped) -&gt; untyped
  def add: (score: untyped) -&gt; untyped
end

class Analyzer
  def self.load: (path: untyped) -&gt; Array[User]
  def self.parse_row: (row: untyped) -&gt; User
end
```

実行してみると、、、

```sh
$ bundle exec steep check --log-level=fatal
bin/main.rb:20:2: NoMethodError: type=singleton(::File), method=open (File.open(fpath, "w") {|fd| fd.write data })
bin/main.rb:22:24: ArgumentTypeMismatch: receiver=singleton(::Analyzer), expected={ :path =&gt; untyped }, actual=::String (fpath)
lib/analyzer.rb:6:2: MethodArityMismatch: method=(self) (def self.load(path))
lib/analyzer.rb:15:2: MethodArityMismatch: method=(self) (def self.parse_row(row))
lib/analyzer.rb:16:8: IncompatibleArguments: receiver=singleton(::User), method_type=(name: untyped, no: untyped) -&gt; ::User (User.new(row["No"], row["Name"]))
lib/user.rb:2:2: MethodArityMismatch: method=initialize (def initialize(no, name))
lib/user.rb:9:2: MethodArityMismatch: method=add (def add(score))
```

エラー増えた…

リポジトリ内の sig/project.rbi も確認してみたのですが、定義の仕方がおかしいわけではなさそうです…&nbsp;

もう少し中身を確認した方がよさそうなので、日を改めて取り組んでいきたいと思います。
