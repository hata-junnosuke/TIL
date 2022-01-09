# 1/8の積み上げ
- 【インスタ】

  - まずはお手本を手元で。しかしなかなかローカルで見れない。
      - cloneしてbundle installした。
     - bin/rails s でCould not load database configuration. No such file - ["config/database.yml"]
      =>database.yml.defaultがある！
      これはdatabase.ymlをgitignoreで管理していることと他の人がcloneして開発するときに自分の環境での設定ができるようにあるとのこと。
     - bin/rails sで`Mysql2::Error::ConnectionError (Can't connect to local MySQL server through socket '/tmp/mysql.sock' (38)):`
       https://qiita.com/k-yasuhiro/items/95a60618d9743e3749a2　 で解決
     - bootstrapが読み込めない　＝＞yarnをインストール
     - redisがなんちゃら　＝＞　redis-surverで立ち上がる
     - なんだかんだ見れたーーーーー。

  - 次は自分の開発
    - rails newの段階でgit flow initをしてmasterとdevelopブランチを自身のパブリックリポジトリに対してプッシュすると良いでしょう。
    - $ bundle init
    - $ Gemfile内の # rails部分のコメントアウトを外し、Railsのバージョンを課題で指定された通りにする。
    - $ bundle install --path vendor/bundle
    - $ bundle exec rails new . -d mysql --skip-coffee --skip-turbolinks
      - ここでmysqlが`An error occurred while installing mysql2 (0.5.3), and Bundler cannot continue.`
        mysql問題　https://qiita.com/tktcorporation/items/0ef8c930fc18ce72c301 攻略
    - $ git add .
    - $ git commit -m "initial commit"
    - $ git flow init (全部EnterでOK。git flowについてはこちらを参照）
    - $ git push --all （これでmasterとdevelopが一気にリモートにプッシュされる）
    - $ git flow feature start 01_login
    - 今日はここまで明日から開発




# 明日のTODO
- 【就活】企業の調べと面接のための準備
- 【インスタ】開発

# コメント


<br/>

---

#  1/9の積み上げ
- 【インスタ】昨日のメモを復習がてら、言語化
インスタメモ
- git flowとは

- turbolinkとは
  - Turbolinksとは、Rails4から正式導入された画面遷移を高速化させるライブラリ
    Turbolinks自体はJavaScriptのライブラリとして提供されているが、Railsでは利用しやすいようにGemとしてデフォルトで組み込まれている。
     - 詳しくは　https://techtechmedia.com/turbolinks-rails/

- slimとは
  - Slim は 不可解にならない程度に view の構文を本質的な部品まで減らすことを目指したテンプレート言語。
    https://github.com/slim-template/slim/blob/master/README.jp.md

- sorceryとは
  - sorceryとは、Railsに認証機能の実装を行うためのライブラリ。
  同じように認証機能を提供してくれているものとしてdeviseなどが挙げられるが、sorceryの方がよりシンプルで、カスタマイズ性に富んでいるという特徴を持つ。
    - https://boku-boc.hatenablog.com/entry/2020/10/10/213625
    - https://qiita.com/d0ne1s/items/f6f8f4cc7ae6eea069fb

- rubocopとは
  - Rubyの静的コード解析を実行するgem。HTML、CSS、またはそれらの中の埋め込みrubyは解析してくれない。
    またRuboCopの警告全てが正しい訳ではなく、どちらかと言うとこうした方が良いよ的なものが多いので、厳しすぎるんじゃないかと思ったらデフォルトを変えたり、警告する/しないの設定をすることが良い。
    https://qiita.com/tomohiii/items/1a17018b5a48b8284a8b

- redisとは
  - Redisとは、Key-Valueでデータを保存する非リレーショナルデータベースで、単純なKey-Valueだけでなく複雑な種類のデータも扱えます。
    また、コンピューターのメインメモリ上でデータを管理しているので、非常に高速にデータへアクセスできるのが特徴です。
    現場Rails7章ではsidekiqを使用するときに使用している。
  - https://www.fenet.jp/infla/column/%E6%9C%AA%E5%88%86%E9%A1%9E/redis%E3%81%A8%E3%81%AF%E3%81%A9%E3%81%AE%E3%82%88%E3%81%86%E3%81%AA%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%EF%BC%9F3%E3%81%A4%E3%81%AE%E7%89%B9%E5%BE%B4%E3%82%84%E4%BD%BF%E3%81%84/

- annotateとは
  - 各モデルのスキーマ情報をファイルの先頭もしくは末尾にコメントとして書き出してくれるGem。
    どんなカラムがあったっけ？ってなった時にいちいちdb/schema.rbを見に行く手間を省ける。
    さらに、config/routes.rbにルーティング情報を書き出してくれる機能もある。 
    https://qiita.com/koki_develop/items/ae6b5f41c18b2872d527

- i18nとは
  - アプリケーションの文言を英語以外の 別の1つの言語に翻訳 する機能や 多言語サポート 機能を簡単かつ拡張可能な方式で導入するためのフレームワークを提供するgem。 
   https://railsguides.jp/i18n.html

- database.ymlとは
  - Railsでデータベースに接続するための情報を記載したファイル。
    rails new <アプリ名>でrailsのプロジェクトを新規作成した時に、configディレクトリ配下に自動生成される。
    デフォルトのDBはsqliteが指定されている。

- migrationファイルとは
  - マイグレーションファイルとは、データベースの設計図。このファイルを実行することにより記述した内容がデータベースに反映される。 
    https://pikawaka.com/rails/migration

- schema.rbとは
  - DBのスキーマをそのままキャプチャしたもの。最新のDBの状態が反映されている。 
    https://qiita.com/shizen-shin/items/84189c16c4d0b2ce8cb4

- config/application.rbとは
  - railsの環境は、開発環境（development）、テスト環境(test)、本番環境(production)の３つがある。これらのすべての環境に共通の設定を記述したファイル。
    https://qiita.com/shizen-shin/items/7466d71613500ef01983

- yarnとは
  - Node.jsのパッケージマネージャー 
    https://qiita.com/akitxxx/items/c97ff951ca31298f3f24
    
# 明日のTODO
- 【インスタ】午前中は開発
```
turbolinkとcoffee-scriptは使わないようにする
slim-railsを導入してビューテンプレートはslimを使う
sorceryを導入してログイン機能を実装する
```
- 【就活】面接練習
  - 車に芋ってしゃべりながら。伝えたいことを整理して実際に喋る。
- 夜は比較的フリーにしておく。
# コメント
今日1時間しかやらなかったと思うか、1時間できたと思うかは自由だ。（現実焦れ）
<br/>

---
# の積み上げ
- 
# 明日のTODO
- 
# コメント

<br/>

---
---
# の積み上げ
- 
# 明日のTODO
- 
# コメント

<br/>

---
