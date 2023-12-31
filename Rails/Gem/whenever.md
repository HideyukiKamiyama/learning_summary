# cronについて

`whenever`の前にまず`cron`について説明します。
`cron`はUNIX系のOSに標準で搭載されている仕組みで、「⚪︎時になったら⚪︎⚪︎のコマンドを実行」といった形で自動でプログラムを実行します。

`cron`を操作するにはcrontabというコマンドを使用します。
以下はコマンドの一例です。
```
# 現在設定中の定期実行タスクの一覧を表示
crontab -l

# cronの削除
crontab -r
```


# wheneverとは？

先ほどのcronをRubyで操作できるようにするためのgemです。
このgemを使うことでRakeタスクのようなプログラムを指定したタイミングや時間毎に実行することができます。


# インストール方法

Gemfileに次のコードを記述し、`bundle install`します。

```ruby
gem 'whenever', require: false
```

`require: false`はgemを自動読み込みをさせないようにするための記述です。
デフォルトでgemは自動で読み込まれるように設定されているためこの記述をしないとアプリを起動した際に自動で読み込まれてしまい、処理が遅くなってしまいます。
`whenever`はコマンドで実行することからアプリで呼び出す必要がないため`require: false`を記述すると良いです。


# 設定ファイルの作成

```
bundle exec wheneverize .
```

上記のコマンドを実行することで`config/schedule.rb`が作成されます。
このファイル内に以下の例のような定期実行したい処理や間隔を記述します。

```ruby
every 3.minutes do
  rake # （実行したいタスク）
end
```

上記の例では１分毎に対象のタスクを実行するよう設定されています。


## `config/schedule.rb`ファイルの編集

```ruby
require File.expand_path(File.dirname(__FILE__) + '/environment')

rails_env = ENV['RAILS_ENV'] || :development

set :environment, rails_env

set :output, "#{Rails.root}/log/cron.log"

# ここよりも上の記述でwhenever実行に必要な記述は終わっているのでここより下に実行したいタスクに関する情報を記述する。
# 以下サンプル
every 3.minutes do
  rake # （実行したいタスク）
end
```

- `require File.expand_path(File.dirname(__FILE__) + '/environment')`

  ファイル内でルートディレクトリを呼び出せるようにするために記述します。
  今回の場合はcronログの記録場所を指定するパスの中で`"#{Rails.root}/log/cron.log"`と記述しているためこの記述が必要になります。

- `rails_env = ENV['RAILS_ENV'] || :development`

  `rails_env`に環境変数をセットしている。`ENV['RAILS_ENV']`が存在する場合は`rails_env`に`ENV['RAILS_ENV']`を代入し、存在しない場合は`:development`を代入している。

- `set :environment, rails_env`

  上記で作成した`rails_env`を使って環境変数をセットしている。


- `set :output, "#{Rails.root}/log/cron.log"`

  cronのログを記録するファイルを指定します。
  先ほど`config/schedule.rb`ファイル内で`Rails.root`を使用することができるようにしているためこのようにパスを指定することが出来ています。
  ログをとっておくことでエラーが発生した場合に参考にできるデータが得られるためこの記述はしておいた方が良いです。


## job_type

`config/schedule.rb`ファイル内でジョブタイプの設定をすることができ、デフォルトでは次の４種類のジョブタイプが使用できます。

- `rake`
  rakeタスクの実行

- `command`
  bashコマンドの実行
  
- `runner`
  Rails内のメソッドの実行

- `script`
  scriptの実行

これらはデフォルトで以下の動きをします。

```
job_type :command, ":task :output"
job_type :rake,    "cd :path && :environment_variable=:environment bundle exec rake :task --silent :output"
job_type :runner,  "cd :path && bin/rails runner -e :environment ':task' :output"
job_type :script,  "cd :path && :environment_variable=:environment bundle exec script/:task :output"
```

上記の４種のジョブタイプに当てはまらない場合は以下のようにして自分でジョブタイプを作成することができます。(job_type_nameは自分で好きな名前を付けられる）

```ruby
# このようにしてjob_typeを設定する
job_type :job_type_name, "パス"
```


# `config/schedule.rb`ファイルの実行、取り消し

`config/schedule.rb`ファイルに定期実行の設定を記述したら次のコマンドでファイルの内容を読み込ませます。

```
$ bundle exec whenever --update-crontab
```

このコマンドにより`config/schedule.rb`ファイル内に記述した内容が読み込まれ、定期実行されるようになります。

また、この定期実行を止めたい場合は以下のコマンドで止めることができます。

```
$ bundle exec whenever --clear-crontab
```

## 設定内容の確認

設定内容にエラーが無いかを確認します。
```
bundle exec whenever
```


# 参考サイト

[github whenever](https://github.com/javan/whenever)

[【Rails】定期実行をしてみよう！（whenever, cron） | ISSEN](https://blog.to-ko-s.com/rails-periodic-execution/)

[未経験からWebエンジニアを目指す [Rails] Rakeタスク、cron、wheneverを使って一時間ごとにタスクを実行](https://osamudaira.com/358/)

[[Rails]Rails5 wheneverでRakeタスクを定期的に実行](https://zenn.dev/yusuke_docha/articles/2d2cfd1030f6ac)

[Railsでwheneverを使ってcronを設定する #Ruby - Qiita](https://qiita.com/Esfahan/items/e7a924f7078faf3294f2)

[【Rails】記事ステータスの追加 #cron - Qiita](https://qiita.com/mmaumtjgj/items/e78747267c28563d6b78)

[【Rails】wheneverでcronを設定 #cron - Qiita](https://qiita.com/mmaumtjgj/items/19e866f31541abb6c614)
