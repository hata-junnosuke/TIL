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
# 1/10の積み上げ
- 【インスタ】
```
## 1/10
- %表記の追加（コロンの削除を忘れずに）
- rubocop追加
- rubocop修正実行（1 offense todoにも残り）
- better_errors
- binding_of_caller
- gem 'pry-byebug'
- gem 'pry-rails'
  - https://pikawaka.com/rails/pry
- gem 'sorcery'
- gem 'slim-rails'
- BMD導入
  - assets/javascript/application.jsを編集
  - stylesheet/application.scssに変更して@import記述
  - package.json記述
  - yarnインストール
- generateコマンド時に生成されるファイルを制限する
- gem 'redis-rails'
  - config/envi/deveに記述したがなんのためか不明
- タイムゾーンの設定
  - config/appに記述
- i18nの基本設定
  - config/appに記述
- jqueryとpopperの追加（ブートストラップのため）
- ログイン機能
  - app.scssにcss記述
  - controller/appにフラッシュタイプを記述
  - rails g sorcery:install
  - db/migrate/sorceryを編集
  - bin/rails db:migrate
  - Userモデルにバリデーション追加
  - user_sessions_controller.rb
  - users_controller.rb
  - ja.ymlで日本語化
  - routes.rb
  - viewを作る
  - ここでローカルでやってみたがフラッシュがエラー？ ActionView::MissingTemplate
  - コメントアウトでやると画面は表示される。
```
- 【就活】面接イメージ

# 明日のTODO
- 【就活】面接イメージ
# コメント
明日から仕事。
<br/>

---
---
# 1/11の積み上げ
- 【インスタ】
```
## 1/11
- 昨日のエラーは解消（なんだったのか、コメントを消したら解決）
- annotaite
  - gem追加
  - bundle exec rails g annotate:install
  - bundle exec annotate
- rubocop
  - bundle exec rubocop -a
```
- 【就活】面接のイメージ
# 明日のTODO
- 【就活】面接
# コメント
スローペース
<br/>

---
---
# 1/12の積み上げ
- 【面接】
```
- 前提
  - 面接官は社長、履歴書と職務経歴書には目を通している模様だが、ポートフォリオには触っていない。
  - 面接時間は50分を超えたくらい。
- 質問内容
  - 雑談
  - 経歴書に沿って紹介をして
    - 自己紹介
    - 現職について
    - プログラミングの学習について何をしてきたか。
    - ポートフォリオについて
  - 現職について質問
    -　これまでで苦労したこと
    -　一番気合入れてやったこと
  - エンジニアを目指す理由（現職からエンジニアへなりたいことがつながらないのでなんで？）
  - エンジニアの人には会ったことありますか。
  - エンジニアの人とは何か違いを感じていますか。
  - ポートフォリオについて
    - 画面共有で説明して
    - GitHubのgemfileのコードを見て、「何を参考にgemを入れているのか」 
  - どういう人とは仕事をしたくないか。
  - 1人でもくもくするのが好きか、チームでやるのが好きか。
  - お酒は好きか？
  - 他に応募している企業はありますか。
  - 質問は終わりで逆質問
  - 締め
    - 結果は1週間から2週間後
    - そのときに採用条件を
    - いつから働けますか。
    - 給料はどれくらい欲しいですか。
    - 上司に「仕事をしたくないであげた人」はいないと。 
    - 仕事は書き入れ時になると忙しい。終電とかまで（あまりおすすめではないが）やる人もいます。とのこと。
- 感想
  -　うちの会社の志望理由は聞かれなかった。
  -　ポートフォリオについて「説明して」と言われてどれくらい喋ればいいのかわからなかった。なので最低限の機能のみで収めた。アピールはもっとしたかった。
  -　自己紹介についても同様で、経歴書に沿ってしゃべっていたが、ある程度でしゃべりすぎていると感じて、ツッコミを一回受けようと切りのいいところでやめた。
  -　GitHubの草とかポートフォリオは実際に使ってもらっていることは受けがいいかと思ったら反応なしだった。
  -　質問なんだっけ。みたいなことがあった。（チーン）
  -　面接は普段やっていること、感じていることを喋るのが良くて、それを最短距離で伝えられることが大事と感じた。  
```
# 明日のTODO
- できるかな。
# コメント
初面接おつ。いい経験。
<br/>

---
---
# 1/13の積み上げ
- 【インスタ】写経終了。機能も解答例と同じく動く。あとはコメントする。
# 明日のTODO
- 【インスタ】コメント提出できるかな。
# コメント
やっと写経できた。データベースなどの環境構築に苦戦した。
<br/>

---
---
# 1/14の積み上げ
- 【インスタ】rubocopの修正、コード分析
# 明日のTODO
- 【インスタ】提出
# コメント
仕事があるけどできることから
<br/>

---
---
# 1/15の積み上げ
- 【インスタ】
```
# 01 ログイン機能の実装
ログイン機能を実装しました。レビューをお願いします。
## 感想及び疑問
- 最初が大変ということを聞いていたが、その通りでした。環境構築に苦戦してかなり時間がかかってしまった。しかし、エラーに対する処置を学べて「このエラーならこう対処する」といったセオリーを作れた。メモも残してあるので今後も使えるかと思われる。
- git flowは実用的なのかどうか。チーム開発をしたらわかるのかとは思うが、今の段階では疑問。
## 実装過程メモ
- %表記の追加（コロンの削除を忘れずに）
- rubocop追加
- rubocop修正実行
- better_errors
- binding_of_caller
- gem 'pry-byebug'
- gem 'pry-rails'
 - https://pikawaka.com/rails/pry
- gem 'sorcery'
- gem 'slim-rails'
- BMD導入
 - assets/javascript/application.jsを編集
 - stylesheet/application.scssに変更して@import記述
 - package.json記述
 - yarnインストール
- generateコマンド時に生成されるファイルを制限する
- gem 'redis-rails'
 - config/envi/devに記述したがなんのためか不明
- タイムゾーンの設定
 - config/appに記述
- i18nの基本設定
 - config/appに記述
- jqueryとpopperの追加（ブートストラップのため）
- ログイン機能
 - app.scssにcss記述
 - controller/appにフラッシュタイプを記述
 - rails g sorcery:install
 - db/migrate/sorceryを編集
 - bin/rails db:migrate
 - Userモデルにバリデーション追加
 - user_sessions_controller.rb
 - users_controller.rb
 - ja.ymlで日本語化
 - routes.rb
 - viewを作る
 - ここでローカルでやってみたがフラッシュがエラー？ ActionView::MissingTemplate
 - コメントアウトでやると画面は表示される。
- エラーは解消（コメントを消したら解決したのでコメントが悪かった？）
- annotaite
 - gem追加
 - bundle exec rails g annotate:install
 - bundle exec annotate
- rubocop
 - bundle exec rubocop -a
- rubocopの修正
- validates :password, length: { minimum: 3 }, if: -> { new_record? || changes[:crypted_password] }の意味
 - new_record?メソッドは、インスタンスが新規に作成されるものかどうかを判定するActive Recordのメソッド
 - if: ->以下は条件付きバリデーションの、シンボルやLambdasてやProcの部分
- config/envi/dev
config.session_store :redis_store, { servers: 'redis://localhost:6379', expire_after: 1.day }
 - rails のセッション管理を redis サーバーで行っている。
 - expires_afterで有効期限を定めている
 - https://ccbaxy.xyz/blog/2020/06/21/ruby47/
## 復習ポイント
- git flowとは
   -   Vincent Driessen氏がブログに書いた"A successful Git branching model" というブランチモデルの導入を簡単にする git プラグイン。
   -   https://qiita.com/KosukeSone/items/514dd24828b485c69a05
   -   https://www.atlassian.com/ja/git/tutorials/comparing-workflows/gitflow-workflow
- turbolinkとは
 - Turbolinksとは、Rails4から正式導入された画面遷移を高速化させるライブラリ
   Turbolinks自体はJavaScriptのライブラリとして提供されているが、Railsでは利用しやすいようにGemとしてデフォルトで組み込まれている。
  - 表現は雑だが他の機能を阻害するなど曲者らしい。
  - https://github.com/turbolinks/turbolinks
  - 詳しくは　https://techtechmedia.com/turbolinks-rails/
- slimとは
 - Slim は 不可解にならない程度に view の構文を本質的な部品まで減らすことを目指したテンプレート言語。
   https://github.com/slim-template/slim/blob/master/README.jp.md
- sorceryとは
 - sorceryとは、Railsに認証機能の実装を行うためのライブラリ。
 同じように認証機能を提供してくれているものとしてdeviseなどが挙げられるが、sorceryの方がよりシンプルで、カスタマイズ性に富んでいるという特徴を持つ。
   - https://github.com/Sorcery/sorcery
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
 - デメリットとしてデータが揮発性、メモリの消費が大きいことがある。
 - https://www.fenet.jp/infla/column/%E6%9C%AA%E5%88%86%E9%A1%9E/redis%E3%81%A8%E3%81%AF%E3%81%A9%E3%81%AE%E3%82%88%E3%81%86%E3%81%AA%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%EF%BC%9F3%E3%81%A4%E3%81%AE%E7%89%B9%E5%BE%B4%E3%82%84%E4%BD%BF%E3%81%84/
- annotateとは
 - 各モデルのスキーマ情報をファイルの先頭もしくは末尾にコメントとして書き出してくれるGem。
   どんなカラムがあったっけ？ってなった時にいちいちdb/schema.rbを見に行く手間を省ける。
   さらに、config/routes.rbにルーティング情報を書き出してくれる機能もある。
  - https://qiita.com/koki_develop/items/ae6b5f41c18b2872d527
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
 - Railsの環境は、開発環境（development）、テスト環境(test)、本番環境(production)の３つがある。これらのすべての環境に共通の設定を記述したファイル。
   https://qiita.com/shizen-shin/items/7466d71613500ef01983
- yarnとは
 - Node.jsのパッケージマネージャー
   https://qiita.com/akitxxx/items/c97ff951ca31298f3f24
```
# 明日のTODO
- 【インスタ】02行けるか？
- 【就活】応募
# コメント
まとめてやれていないが少しづつ。
<br/>

---
# 1/16の積み上げ
- 【インスタ】
```
### 1/16
- シードファイルとは
  - 簡単に言うと初期データのこと
  - https://qiita.com/takehanKosuke/items/79a66751fe95010ea5ee
- fakerとは
  - RSpecなどでテストする際に、FactoryBotなどであらかじめデータを用意することがあるが、その際にランダムデータをいい感じに作ってくれるgem
  - seedファイルなんかでも使えば初期データをランダムにいろいろ作成できる。
  - https://github.com/faker-ruby/faker
  - https://qiita.com/koki_73/items/60c2441fb873a8db35d5
- carrierwaveとは
  - CarrierWaveとは、ファイルのアップロード機能を簡単に追加する事が出来るgem
  - https://pikawaka.com/rails/carrierwave
  - https://github.com/carrierwaveuploader/carrierwave
- image_magickとは
  - 画像を操作したり表示したりするためのソフトウェア
  - https://qiita.com/wann/items/99e6de5a8c0ba7b959e8
- Swiperとは
  - Swiper.jsはスライダーが作れるJavaScriptライブラリ
  - https://kiomiru.co.jp/blog/coding/swiper-js/
  - https://swiperjs.com/get-started
- fontawesomeとは
  - Web上にはさまざまなアイコンを無料で用意してくれている便利なサイト
  - https://qiita.com/koki_73/items/fa6d31a3b54a84d19dcf
  - https://fontawesome.com/
```
- 【就活】応募
# 明日のTODO
- 【インスタ】crud作成コードを写経
# コメント
応募しすぎて捌けなくなるのは避けよう。とりあえず水曜まで返事まつ。
<br/>

---
# 1/17の積み上げ
- 【インスタ】
```
### 1/17
- Postsモデル作成（テストができてる。。）
- faker,carrierwaveの導入
- swiperの導入
  - `yarn add swiper`
  - `yarn install`
  - https://qiita.com/ken_ta_/items/bdf04d8ecab6a855e50f
  - みけたさんの記事 https://qiita.com/miketa_webprgr/items/0a3845aeb5da2ed75f82
- routes.rbの編集(この時点ではviewを作っていないのでエラー発生)
- エラー発生
  - Sprockets::FileNotFound
  - 解決はassets/javascripts/swiper.jsを作成して中身を写経したら治った。
  - これは何かのコマンドで作成される？手動が正解？
- アップロードファイルをgit管理対象外にした
```
- 【就活】応募からの返信待ち
# 明日のTODO
- 【インスタ】02終わらせたい。提出したい。
# コメント
着実に着実に。
<br/>

---
# 1/18の積み上げ
- 【インスタ】
```
### 1/18
- コントローラー作成
  - `$ bin/rails g controller posts index show new edit create destroy update`
  - 上でやるとcreateなどのビューもできてしまうミス
- ビューの作成とcss
- carrierwave
  - bundle exec rails g uploader post_image
- CRUD実装
- Swiper
- ルート修正


### TODO
- モデル作成時にテストが作成されている。
- routesの順番変えたのはなぜ？
- スマホ対応　
  - `meta[name="viewport" content="width=device-width, initial-scale=1.0"]`
- constraints ->  request { request.session[:user_id].present? } do
- cssの確認
- post、userモデルの確認
- publicに入れ方
- swiper
```
- 【就活】CABとは。その解き方。

# 明日のTODO
- 【インスタ】　TODOの理解と考察
- 【就活】CAB練習　https://saisokuspi.com/cab/
# コメント
うーん採用厳しそう。
<br/>

---
# 1/19の積み上げ
- 【インスタ】
```
- モデル作成時にテストが作成されている。
  - `g.test_framework false`をconfig/appに記述することで解決
  - https://qiita.com/kodai_0122/items/14494a3848654f32909d
- routesの順番変えたのはなぜ？
  - おそらくresourcesをまとめた。
- スマホ対応　
  - `meta[name="viewport" content="width=device-width, initial-scale=1.0"]`
  - この記述によって画面の大きさによってサイズを変更していることになる。
  - https://qiita.com/pagk/items/ef3eabe0e9f7fe885284
- constraints ->  request { request.session[:user_id].present? } do
  - https://railsguides.jp/routing.html#:~:text=3.9%20%E3%83%AA%E3%82%AF%E3%82%A8%E3%82%B9%E3%83%88%E5%86%85%E5%AE%B9%E3%81%AB%E5%BF%9C%E3%81%98%E3%81%A6%E5%88%B6%E9%99%90%E3%82%92%E5%8A%A0%E3%81%88%E3%82%8B
  - https://blog.piyo.tech/posts/2018-03-31-routes-constraints/
  - constraintsで制限を加えることができる。
  ```
# 明日のTODO
- 
# コメント

<br/>

---
# 1/22の積み上げ
- 【インスタ】
```
- `require_login`ってどこかで書いたっけ？
  - sorcery.controllerに入っている。
  - 同様にlogin,current_userなどもここに記述があった。
- 複数の画像を保存する
  - https://qiita.com/tanutanu/items/47f8a229ef52cae3c251
  - モデルにserialize :images, JSON
  - コントローラーにimages: []
  - ビューにmultiple: true
- kaminari
  - https://github.com/kaminari/kaminari
  - ポストコントローラー
  - `bundle exec rails g kaminari:views bootstrap4 -e slim`
  - `rails g kaminari:config`
  - config/ini/kaminari~作成（10件になっているのを15に）
```
- git flowについて
  - https://tracpath.com/bootcamp/learning_git_git_flow.html
- 【チェリー】12章デバックの読了
# 明日のTODO
- 【インスタ】回答作成
# コメント
速度まし。
<br/>

---
# 1/23の積み上げ
- 【インスタ】
```
### 1/23
- shallowルーティングとは
  - https://qiita.com/tanutanu/items/a245f7691c77b56d4cd3
  - ネストしたルーティングにおいて、下層にあるテーブルのIDが一意なら、その上にあるテーブルのIDは不要という発想に基づいて、 edit、show、update、destroyの４アクションで、URIパターンがスッキリし、ヘルパーメソッドも短くなったルーティング手段。

- ` bundle exec rails g model comment user:references post:references body:text`
- `bundle exec rails db:migrate`
- ` bundle exec rails g controller comments `
- post/showにcommentのインスタンス記述
- userとpostモデルに関連づけ
- viewの作成
- shallowルーティング

## TODO
- `.merge(post_id: params[:post_id])`
  - https://qiita.com/zenfumi/items/694edc863527f91c8976
  - commentモデルに関して送られるパラメータ以外にpost_idを含めたので今回使用している。
- `.build(comment_params)`
  - https://qiita.com/ryosuke-endo/items/6bae532b4f678fdcf87d
  - ここは二つのモデルを結合する時にはbuildを使うのが慣習らしい。（QAアプリでもやったぞ）
- partialあたりを理解する
  - https://pikawaka.com/rails/partial_template
  - partialオプション、localオプションもあるということを学習。
- modalも確認
  - https://www.sejuku.net/blog/78105
  - すごく雑に書くと
    - modal-header」で画面名やタイトルを書くヘッダー部分
    -「modal-body」で詳細な内容を書くボディ部分
    -「modal-footer」でボタンなど置くフッター部分
- bigint
  - integerで作成してしまうとforeign keyが作成できない。そのため、テーブルのカラムをbigintで作成する必要がある。
  - なお、rails5よりidに対してデフォルトらしい。
- javascriptが機能していない。renderがうまくいかない。
  - ファイル名の最後にインデントがあって反応しなかった。
- bootstrapのcss
  - https://webnetamemo.com/coding/bootstrap4/201906138320
```

- 【今日の疑問】
- `bin/rails~`と`bundle exec rails ~`の違い
- `git add .`の.の意味
# 明日のTODO
- `bin/rails~`と`bundle exec rails ~`の違い
- `git add .`の.の意味
- 【インスタ】5の準備とこれまでをいったん復習
# コメント
明日は時間をあまり取れなさそうだが、やることに意味がある。
<br/>

---
# 1/24の積み上げ
- 【git】
  - プルリクを間違えてマージしてしまった時の対処
    - https://saikeblog.com/2020/03/09/github%E3%81%A7pull-request%E3%81%AE%E3%83%9E%E3%83%BC%E3%82%B8%E3%82%92%E6%89%93%E3%81%A1%E6%B6%88%E3%81%99%E6%96%B9%E6%B3%95/#:~:text=Github%E3%81%A7Pull%20Request%E3%81%AE%E3%83%9E%E3%83%BC%E3%82%B8%E3%82%92%E6%89%93%E3%81%A1%E6%B6%88%E3%81%99%E3%81%AB%E3%81%AF,%E6%89%93%E3%81%A1%E6%B6%88%E3%81%99%E3%81%93%E3%81%A8%E3%81%8C%E3%81%A7%E3%81%8D%E3%81%BE%E3%81%99%E3%80%82
  - プルリクのベースブランチを変更するときのやり方
    - https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/changing-the-base-branch-of-a-pull-request
- 【インスタ】いいねまで写経終了
```
### 1/24
- まずはいいね機能の実装方法を確認
  - https://techtechmedia.com/favorite-function-rails/
- `has_many :like_users, through: :likes, source: :user`これはどゆこと
  - https://qiita.com/tanutanu/items/2a27f1e503c4cbf30928
  - 別名のカラムを作成して中間テーブルの役割をしている
- `bundle exec rails g model like user:references post:references`
- t.index [:user_id, :post_id], unique: trueでダブり防止
- `bundle exec rails db:migrate`
- likeモデルでもバリデーション
- userとpostに関連づけとメソッド追加
-  bundle exec rails g controller likes
- view作成
- routes
```
# 明日のTODO
- 【インスタ】考察して提出
# コメント
コードで気になったらどうしてかを調べよう。わからなければ聞こう。
<br/>

---
# の積み上げ
- 
# 明日のTODO
- 
# コメント

<br/>
