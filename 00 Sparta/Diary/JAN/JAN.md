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
# 1/25の積み上げ
- 【インスタ】フォロー機能の写経完了
```
### 1/25
- user一覧と詳細
  - `render @user`ってドユコト
- post一覧編集
- relationshipモデル作成
  - 関連づけ
    - # :userじゃないの？
      belongs_to :follower, class_name: 'User'
      belongs_to :followed, class_name: 'User'
- userモデルと関連づけ
- relationshipコントローラ作成
- relationshipビュー作成
- routes
```
# 明日のTODO
- 【インスタ】フォローの検索、次の課題も提出
# コメント
緊急事態で時間が取れそう。インスタは来週の週末には終わらせる。
<br/>

---
# 1/26の積み上げ
- 【インスタ】
```
### 1/26
- 考察
  - has_many :following, through: :active_relationships, source: :followed
    has_many :followers, through: :passive_relationships, source: :follower
    - https://www.youtube.com/watch?v=gATeEnr8gh4
      - 上はフォローしている人をactiveを通じてfollowed(フォローされている人)から持ってくる。
      - 下はフォロワーをpassiveを通じてfollower(フォローしている人)持ってくる。
  - ` Post.where(user_id: following_ids << id)`なぜこれで自分の投稿まで取得できる？
    - https://qiita.com/miketa_webprgr/items/361d339d2739792457ab
    - selfが隠れている！！
検索
- 実装方法を考察
  - https://techacademy.jp/magazine/22330
  - しかし実装方法が結構違うので、解答例のコミットを追いながら学習する。
- route collectionブロック
  - （Railsガイド）GETリクエスト+/photos/searchなどの（idを伴わない）パスを認識し、リクエストをPhotosコントローラのsearchアクションにルーティングします。このときsearch_photos_urlやsearch_photos_pathルーティングヘルパーも同時に作成されます。
- 検索のスコープをpostモデルに追加
- 検索用のコントローラ
- 検索用のビュー
- コメントとユーザーからも検索できるようにする
- 


## 疑問
- FormObject、search_postのあたりの理解
  - https://tech-essentials.work/questions/160
  - https://tech-essentials.work/courses/11/tasks/15/outputs/54
  - strip..文字列先頭と末尾の空白文字を全て取り除いた文字列を生成して返します。
  - .split(/[[:blank:]]+/)は空白で区切っている。
  - ActiveModel,ActiveRecordそれぞれの機能
- .fetchとは？
  - fetchメソッドは引数にハッシュのキーを指定することにより、そのキーとセットになっているバリューを取り出します。
  - fetchメソッドでは第二引数にデフォルトのバリューを設定することができます。
  - application_controllerで空のハッシュをデフォルトとしている。
- injectとは？
  - https://www.sejuku.net/blog/19219
  - injectはeachやmapと同じように繰り返しを行うメソッドです。
  - 配列オブジェクト.inject {|初期値, 要素| ブロック処理 }
- ransackの偉大さを感じる
```
# 明日のTODO
- 【インスタ】2つの課題をクリア
# コメント
予期しない時間ができた。ラッシュかける。
<br/>

---
# 1/27の積み上げ
- 【復習】
  -  リレーションの復習
    - フォローする側（following）とされる側（follower）を別のモデルと捉えるといいのかな。わかったようなわからないような
    [![Image from Gyazo](https://i.gyazo.com/e9fbb679270fb137aee711cc08c30d89.png)](https://gyazo.com/e9fbb679270fb137aee711cc08c30d89) 
    - https://qiita.com/kazukimatsumoto/items/14bdff681ec5ddac26d1#%E3%83%95%E3%82%A9%E3%83%AD%E3%83%BC%E3%83%95%E3%82%A9%E3%83%AD%E3%83%AF%E3%83%BC%E6%A9%9F%E8%83%BD%E3%82%92er%E5%9B%B3%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E8%A8%AD%E8%A8%88%E3%81%97%E3%82%88%E3%81%86
- 【インスタ】
 ```
  ## 開発メモ
  ## 1. まずはUserモデルにアバター用のカラムを追加する
      - ` bundle exec rails g migration AddAvatarToUsers`
      - マイグレーションファイルを変更
      ```
      #  db/migrate/20220127042444_add_avatar_to_users.rb
        class AddAvatarToUsers < ActiveRecord::Migration[5.2]
          def change
            add_column :users, :avatar, :string
          end 
        end
      ```
      - `bundle exec rails db:migrate`

  ## 2. 編集画面は/mypage/account/editというパスとする
  - routes.rb

      namespace :mypage do
          resource :account, only: %i[edit update]
      end
  >ルーティングについてはこちらを参考にする
  >https://tech-essentials.work/movies/1

  ### なぜnamespace :mypageを使うのか
  - users_controllerでeditを設定すればいいのではないかと思った。
  - https://github.com/miketa-webprgr/TIL/blob/master/11_Rails_Intensive_Training/09_issue_note.md
  >mypageディレクトリ以下にコントローラファイルやビューファイルが保存されるようになる。
  >管理者画面を実装する場合、「管理者であれば〜を表示したい」ということや、
  >「管理者であれば〜という機能を実装したい」などということが多いかと思う。
  >そのような場合、ディレクトリを分けることなく、if文などを使って対応することもできるが、
  namespaceを使って、ディレクトリを分ける方がスマートである。

  とのこと。
  ## 3. 画像のセッティング（carrierwave）
  公式　https://github.com/carrierwaveuploader/carrierwave#getting-started
  1. `rails generate uploader Avatar`
  2. app/uploaders/avatar_uploader.rbが作成される
  3. avatar_uploader.rbの設定を行う（基本的には、コメントアウトを戻す・修正する形で行える）
  4. Carrierwaveが使えるように、User.rbファイルに追記を行う

        #user.rb
        # 画像セットのため追記
        mount_uploader :avatar, AvatarUploader 
  ## 4. プロフィール編集画面の実装
  ### コントローラー
  - 編集画面しか実装しないので、editアクションとupdateアクションの設定
  - `.permit(:email, :username, :avatar, :avatar_cache)`について、編集画面では`:username`と`:avatar`のみでいいが、今後を見据えて他のものも入っていると思われる。

    ```
    #mypage/accounts_controller.rb
    class Mypage::AccountsController < Mypage::BaseController
      def edit
        @user = User.find(current_user.id)
      end

      def update
        @user = User.find(current_user.id)
        if @user.update(account_params)
          redirect_to edit_mypage_account_path, success: 'プロフィールを更新しました'
        else
          flash.now['danger'] = 'プロフィールの更新に失敗しました'
          render :edit
        end
      end

      private

      def account_params
        params.require(:user).permit(:email, :username, :avatar, :avatar_cache)
      end
    end
    ```
  ### mypage/base_controller.rbの設定
  - いわば、mypage版のapplication_controller.rbと思ってもいい。（と思う。）
    ```
    class Mypage::BaseController < ApplicationController
      before_action :require_login
      layout 'mypage'
    end
    ```
  ### ビューの実装
  - application.html.slimのような立ち位置でlayout/mypage.html.slimを作成
      ```
      /layouts/mypage.html.slim

      doctype html
      html
        head
          meta content=("text/html; charset=UTF-8") http-equiv="Content-Type" /
          meta[name="viewport" content="width=device-width, initial-scale=1.0"]
          title マイページ | InstaCloneApp
          = csrf_meta_tags
          = csp_meta_tag
          = stylesheet_link_tag 'mypage', media: 'all'
          = javascript_include_tag 'mypage'
        body
          = render 'shared/header'
          = render 'shared/flash_messages'
          main
            .container
              .row
                .col-md-8.offset-md-2
                  .card
                    .card-body
                      .row
                        .col-md-3
                          = render 'mypage/shared/sidebar'　　#この後作成
                        .col-md-9
                          .mypage_content
                            = yield　　　　#これでedit.html.slimを入れて表示させる
      ```
  - mypage/shared/sidebarを作成
  - edit.html.slimを作成

  ### プレビューの実装
  - assets/javascripts/mypage.jsにおいてJavascriptでFileAPIっていうのを使っているらしい。
  > [JavaScript FileAPIについて学ぶ \- Qiita](https://qiita.com/kodokunadancer/items/8028d87d8d2bc6c00e69)
  - ちょっとここは後回しにして理解しようと思います。

  ## 5. 細かい修正
  - locales/ja.ymlに追加(avatar: 'アバター)
  - 自分のページであれば編集ページへのリンクを表示
  - デフォルト画像のところを編集
  - ユーザー一覧の上を編集画面のリンクへ
  - &.で未ログイン時に編集画面に行ったときのエラー発生を防ぐ
      - `if current_user&.id == @user.id`
  -  rubocop

  ## コメント
  - /mypage/account/editを使うメリットはスマートということ以外にeditのviewにいくURLにidが含まれないこと（セキュリティー面）なのかなと思いました。
  - mypage/account_controllerは一気に作れないのでしょうか。（rails g controllerのように）
  - FileAPIについては理解に時間がかかりそうなので後回しで先に進もうと思います。
  - みけたさんのノートを参考に理解させていただきました。ありがとうございました。
  >参考
  >https://github.com/miketa-webprgr/TIL/blob/master/11_Rails_Intensive_Training/09_issue_note.md
```
# 明日のTODO
- 【インスタ】
  - 一回復習
  - 通知やろう
# コメント
ちょっと飛ばしすぎたかも
<br/>

---
# 1/28の積み上げ
- 【ルーティング】
  - https://github.com/hata-junnosuke/TIL/blob/main/00%20Sparta/column/%E3%83%AB%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0.md
- 【Formオブジェクト】
  - https://github.com/hata-junnosuke/TIL/blob/main/00%20Sparta/column/Form%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88.md 
- 【インスタ】通知
  - キーワードを調べる
   ```
    # 開発メモ
      ### 0. 予習ポイント
      解答例のコードから初めて見るものを事前学習をする。
      - 通知機能はどう実装する？
      >https://qiita.com/ytoo14/items/2db1dd4fcd7945b980f7 (実装形式は違うが参考までに)
      - ポリモーフィック関連とは？
        - ポリモーフィック関連付けを使うと、ある1つのモデルが他の複数のモデルに属していることを、1つの関連付けだけで表現できます。
      >https://railsguides.jp/association_basics.html#%E3%83%9D%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%95%E3%82%A3%E3%83%83%E3%82%AF%E9%96%A2%E9%80%A3%E4%BB%98%E3%81%91
      - `enum action_type: { commented_to_own_post: 0, liked_to_own_post: 1, followed_me: 2 }`
        - これはaction_typeを定義している。（○○なら1みたいに）
      - `after_create_commit :create_activities`
        - after_create_commitはcreateをすると指定したアクションが発動することを表す
        > https://railsguides.jp/active_record_callbacks.html#:~:text=%E5%AE%A3%E8%A8%80%E3%81%A7%E3%81%8D%E3%81%BE%E3%81%99%E3%80%82-,10%20%E3%83%88%E3%83%A9%E3%83%B3%E3%82%B6%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%AB%E3%83%90%E3%83%83%E3%82%AF,-%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E3%81%AE%E3%83%88%E3%83%A9%E3%83%B3%E3%82%B6%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3
      - gem 'letter_opener_web'(このgemは最終的にはこの課題では導入されていない)
        - letter_openerに送信された電子メールを閲覧するためのインターフェースを提供します。(とりあえずメールに関するgemと理解)
        >https://qiita.com/tanutanu/items/c6193c4c2c352ac152ec
   ```
# 明日のTODO
- 【インスタ】通知の続き
# コメント
楽しくなってきた。

<br/>

---
# 1/29の積み上げ
- 【git】初めてのgit stash
>https://qiita.com/chihiro/items/f373873d5c2dfbd03250

- 【mysql】若干詰まったので
> https://qiita.com/katsuyuki/items/5ba39293f16f5d6cbb56

- 【インスタ】通知の写経まで
# 明日のTODO
- 【インスタ】通知の理解
# コメント
ちょっと詰まり気味
<br/>

---
# 1/30の積み上げ
- 【インスタ】通知課題提出
  - https://github.com/hata-junnosuke/TIL/blob/main/00%20Sparta/column/%E3%82%A4%E3%83%B3%E3%82%B9%E3%82%BF%E9%80%9A%E7%9F%A5.md
# 明日のTODO
- 【インスタ】11？
# コメント
来週末にはインスタ完成目指して。
<br/>

- やることリスト
- ルーティングについてまとめるcollection,menber
- 再度コードレビューしてわからなければ調べる
  - インスタクローンはなぜredis
