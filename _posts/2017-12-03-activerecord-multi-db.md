---
layout: post
title:  "ActiveRecordを使って複数DBにアクセスする(その１)"
date:   2017-12-03 23:35:00 +0900
tags: [ruby]
---
この記事は、<a href="https://qiita.com/advent-calendar/2017/kcscarrot" target="_blank">株式会社ケーシーエスキャロット アドベントカレンダー</a>の２日目の記事です。

ちょっと公開が遅くなってしまいましたが、やっていきます。

１日目に準備をしたので、今日はいよいよプログラムを書いていきましょう。  
以下のような構成で作成していきます。

<div class="separator" style="clear: both; text-align: center;"><a href="https://3.bp.blogspot.com/-U5cEcPMAUCg/WiK7G2WG6oI/AAAAAAAAK2I/NCCT-q_8ri4nHode-Wnq9ICE-y4zCo0iACLcBGAs/s1600/dir_structure.jpg" imageanchor="1" style="margin-left: 1em; margin-right: 1em;"><img border="0" src="https://3.bp.blogspot.com/-U5cEcPMAUCg/WiK7G2WG6oI/AAAAAAAAK2I/NCCT-q_8ri4nHode-Wnq9ICE-y4zCo0iACLcBGAs/s320/dir_structure.jpg" width="290" height="320" data-original-width="527" data-original-height="582" /></a></div><br />

### config/database.yml

own(development) / other(other_development) のKEYで接続情報を記載します。

```ruby
default: &default
  adapter: mysql2
  encoding: utf8
  pool: 5
  username: root
  password:
  socket: /var/lib/mysql/mysql.sock

development:
  <<: *default
  database: multi_connect_development

other_development:
  <<: *default
  database: other_multi_connect_development
```

### lib/common/user.rb

own /other のUserクラスの親クラスとなるクラスです。

同じ構造のテーブルにアクセスするので、共通のクラスを作って、  
このクラスは、AciveRecord::Baseを継承します。

```ruby
class Common::User < ActiveRecord::Base
end
```

lib/own/user.rb, lib/other/user.rb のそれぞれのデータベースにアクセスするためのクラスとしています。

### lib/own.rb

lib/own.rb には、autoload でUserクラスの読み込みと、DBの情報を返す connectメソッドを定義しておきます。

```ruby
module Own
  autoload :User, 'own/user'

  def connect
    MultiConnectConfig.config["development"]
  end
  module_function :connect
end
```

DB接続は、establish_connection をモデルクラスに記載するとできるとの情報を見つけたので、  
lib/own/user.rb には、establish_connection を記載します。

### lib/own/user.rb

```ruby
class Own::User < Common::User
  establish_connection Own::connect
end
```

また、other 側も同様に記載しておきます。

### bin/multi_connect.rb

実際の呼び出すプログラムは、以下のように書きました。  
config/database.ymlを管理するクラス(MultiConnectConfig)を記載しています。  
(別ファイルにすることが望ましいです)

そして、Own側とOther側のUserの１件目をそれぞれ呼び出すプログラムです。

```ruby
class MultiConnectConfig
  file_path = "./config/database.yml"
  @@config = YAML.load_file(file_path)

  def self.config
    @@config
  end
end

p Own::User.first
p Other::User.first

```
## 結果

以下のエラーになりました。  
(2.5からbacktraceの出力が逆になる予定で、この2.5-devのバージョンも逆になっています

```sh
[vagrant@rg batch]$ bundle exec ruby bin/multi_connect.rb
Traceback (most recent call last):
        14: from bin/multi_connect.rb:19:in `<main>'
        13: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/querying.rb:3:in `first'
        12: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/relation/finder_methods.rb:122:in `first'
        11: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/relation/finder_methods.rb:531:in `find_nth'
        10: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/relation/finder_methods.rb:538:in `find_nth_with_limit'
         9: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/relation/delegation.rb:44:in `primary_key'
         8: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/attribute_methods/primary_key.rb:74:in `primary_key'
         7: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/attribute_methods/primary_key.rb:88:in `reset_primary_key'
         6: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/attribute_methods/primary_key.rb:74:in `primary_key'
         5: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/attribute_methods/primary_key.rb:86:in `reset_primary_key'
         4: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/attribute_methods/primary_key.rb:98:in `get_primary_key'
         3: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/model_schema.rb:331:in `table_exists?'
         2: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/connection_handling.rb:88:in `connection'
         1: from /vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/connection_handling.rb:116:in `retrieve_connection'
/vagrant/adventcalendar/batch/vendor/ruby/2.5.0/gems/activerecord-5.1.4/lib/active_record/connection_adapters/abstract/connection_pool.rb:930:in `retrieve_connection': No connection pool with 'primary' found. (ActiveRecord::ConnectionNotEstablished)
```
**No connection pool with 'primary' found.**  
primary って何でしょう？ 次はこのエラーを見ながら、進めていきます。

そして３日目は、naokishi の 「資格取得のススメ(仮)」です。

naokishi と一緒に青物横丁で仕事していたのは...もう17年くらい前!?  
チームが変わって、同じプロジェクトで仕事することはなくなったけれど、  
いい意味で「サラリーマン気質」で、いつもみんなを気遣ってくれるのは、  
あの頃も今も変わらずです。

最近は、お互い日本酒党になったので、また美味しいお酒飲み行きましょう～
