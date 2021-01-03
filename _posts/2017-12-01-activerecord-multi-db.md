---
layout: post
title:  "ActiveRecordを使って複数DBにアクセスする(準備編)"
date:   2017-12-01 06:00:00 +0900
categories: [ruby]
tag: [activerecord]
---
この記事は、<a href="https://qiita.com/advent-calendar/2017/kcscarrot" target="_blank">株式会社ケーシーエスキャロット アドベントカレンダー</a>の１日目の記事です。

Rubyの部署に所属しているemorimaです。

今年は、会社支援で <a href="http://rubykaigi.org/2017/" target="_blank">RubyKaigi 2017</a> と、<a href="http://2017.rubyworld-conf.org/ja/" target="_blank">Ruby World Conference</a>に参加させてもらいました。

また、株式会社ケーシーエスキャロットは、10月に開催した <a href="http://railsgirls.com/tokyo" target="_blank">Rails Girls Tokyo 8th</a> でもスポンサーや、<a href="https://asakusarb.esa.io/" target="_blank">Asakusa.rb</a> の会場にもなっております。

Rubyの会社ではなく、組み込み系やスマフォアプリ作成の仕事もしています。  
昨年から、ETロボコンにも参加している社員もいるので、この後のどこかでお話が聞けるかな～

さて、これから、ActiveRecordを使って複数DBにアクセスするプログラムを作っていこうと思います。  
前に作った時に、うまく接続が切り替わらずに、色々調べることになったので、  
その内容を書いていこうと思います。

今日は準備から。

Databaseの作成は、Railsを使って簡単に作ってしまいましょう！

### 環境

  --- | ---
  Ruby | ruby 2.5.0dev (2017-11-30 trunk 60945) [x86_64-linux]
  Rails | 5.1.4
  Database | mysql  Ver 15.1 Distrib 5.5.56-MariaDB, for Linux (x86_64)

### データベース作成

rails コマンドで、新規アプリを作ります。

```sh
$rails new multi_connect -d mysql
$cd multi_connect
```

Gemfile 上の 18行目の uglifier のコメントを外して、users テーブルを作ります。

```sh
$rails g scaffold user name:string
$rails db:create
$rails db:migrate
```

別のDatabaseを作ります。

config/database.yml のdatabase定義を変更して、
rails db:create, rails db:migrate を実行します。

そして、2つのデータベースにデータを登録しておきます。  
同じデータを登録すると、正しく接続したかがわからないため、  
私は、'taro', 'jiro' で分けて登録しました。

#### データベース1

```sh
MariaDB [multi_connect_development]> select * from users;
+----+--------+---------------------+---------------------+
| id | name   | created_at          | updated_at          |
+----+--------+---------------------+---------------------+
|  1 | taro-1 | 2017-11-30 11:44:53 | 2017-11-30 11:44:53 |
|  2 | taro-2 | 2017-11-30 11:44:53 | 2017-11-30 11:44:53 |
+----+--------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

#### データベース2

```sh
MariaDB [other_multi_connect_development]> select * from users;
+----+--------+---------------------+---------------------+
| id | name   | created_at          | updated_at          |
+----+--------+---------------------+---------------------+
|  1 | jiro-1 | 2017-11-30 11:45:15 | 2017-11-30 11:45:15 |
|  2 | jiro-2 | 2017-11-30 11:45:15 | 2017-11-30 11:45:15 |
+----+--------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

さて、準備はできました。  
次は、実際に接続するRubyプログラムを書いていきましょう。

明日は、「ActiveRecordを使って複数DBにアクセスする(その1)」になります。
