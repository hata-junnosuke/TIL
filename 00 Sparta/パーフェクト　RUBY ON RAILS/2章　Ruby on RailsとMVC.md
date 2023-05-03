# 2章　Ruby on RailsとMVC
## MVCアーキテクチャ
- M・・Model データベースとの接続とデータに対する操作、及びビジネスロック
- V・・View Modelの内容を参照し視覚表現を行う部分
- C・・Controller Modelのロジックを呼び出し、必要なViewの選択など、ModelとViewをつなぐ部分

## ActiveRecordでモデルを実装する
`$ bin/rails g model Book`
これで作成されるマイグレーションファイルの中身はテーブルの定義をActiveRecord：：MigrationのDSLで記述されたもの。


※DSLとはドメイン固有の言語で独自の文法があるというイメージ
> https://qiita.com/getty104/items/b3fcc1f86846fb86f168
### ActiveRecordによるモデルの役割
1. データベースと接続し、データベースのレコードとActiveRecordオブジェクトを結びつける役割（これによりモデルクラスに記述がなくても自動で反映する機能はSQLの抽象化、接続情報の隠蔽につながる）
2. ビジネスロックの実装的な振る舞いに関するところ、すなわちバリデーションやレコード保存時などに実行するさまざまなコールバックなどを実行する役割

## モデルを扱う
### モデルを通じて検索を行う
- コンソールでデータを作成する
```ruby
$ bin/rails c
Loading development environment (Rails 7.0.2.2)
irb(main):001:1* (1.
irb(main):002:1*   Book.
irb(main):002:2*   Book.create(
irb(main):003:2*     name:"Book #{i}",
irb(main):004:2*     published_on: Time.parse("20191224").ago(i.months),
irb(main):001:1* (1..5).each do |i|
irb(main):002:2*   Book.create(
irb(main):003:2*     name:"Book #{i}",
irb(main):004:2*     published_on: Time.parse("20191224").ago(i.months),
irb(main):005:2*     price:(i*1000),
irb(main):006:1*   )
irb(main):007:0> end
   (1.6ms)  SELECT sqlite_version(*)
  TRANSACTION (0.1ms)  begin transaction                                                                                 
  Book Create (0.7ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Book 1"], ["published_on", "2019-11-24"], ["price", 1000], ["created_at", "2022-02-23 01:31:16.979898"], ["updated_at", "2022-02-23 01:31:16.979898"]]                                 
  TRANSACTION (0.4ms)  commit transaction                                                                                
  TRANSACTION (0.0ms)  begin transaction                                                                                 
  Book Create (0.2ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Book 2"], ["published_on", "2019-10-24"], ["price", 2000], ["created_at", "2022-02-23 01:31:16.984624"], ["updated_at", "2022-02-23 01:31:16.984624"]]
  TRANSACTION (0.3ms)  commit transaction
  TRANSACTION (0.0ms)  begin transaction
  Book Create (0.2ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Book 3"], ["published_on", "2019-09-24"], ["price", 3000], ["created_at", "2022-02-23 01:31:16.985846"], ["updated_at", "2022-02-23 01:31:16.985846"]]
  TRANSACTION (0.3ms)  commit transaction
  TRANSACTION (0.0ms)  begin transaction
  Book Create (0.2ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Book 4"], ["published_on", "2019-08-24"], ["price", 4000], ["created_at", "2022-02-23 01:31:16.987211"], ["updated_at", "2022-02-23 01:31:16.987211"]]
  TRANSACTION (0.3ms)  commit transaction
  TRANSACTION (0.0ms)  begin transaction
  Book Create (0.2ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["name", "Book 5"], ["published_on", "2019-07-24"], ["price", 5000], ["created_at", "2022-02-23 01:31:16.988484"], ["updated_at", "2022-02-23 01:31:16.988484"]]
  TRANSACTION (0.3ms)  commit transaction
=> 1..5
```
これを題材に検索する。
- find (idで検索)とfind_by（他で検索）
```ruby
irb(main):008:0> Book.find(1)
  Book Load (0.8ms)  SELECT "books".* FROM "books" WHERE "books"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
=>                                                              
#<Book:0x00007fd7c3082a30                                       
 id: 1,                                                         
 name: "Book 1",                                                
 published_on: Sun, 24 Nov 2019,                                
 price: 1000,                                                   
 created_at: Wed, 23 Feb 2022 01:31:16.979898000 UTC +00:00,    
 updated_at: Wed, 23 Feb 2022 01:31:16.979898000 UTC +00:00>    
irb(main):009:0> B
irb(main):009:0* Book.
irb(main):009:0> Book.find_by(name: "Book 3")
  Book Load (0.6ms)  SELECT "books".* FROM "books" WHERE "books"."name" = ? LIMIT ?  [["name", "Book 3"], ["LIMIT", 1]]
=>                                                              
#<Book:0x00007fd7c318a838                                       
 id: 3,                                                         
 name: "Book 3",                                                
 published_on: Tue, 24 Sep 2019,                                
 price: 3000,                                                   
 created_at: Wed, 23 Feb 2022 01:31:16.985846000 UTC +00:00,    
 updated_at: Wed, 23 Feb 2022 01:31:16.985846000 UTC +00:00>  
 ```
- findは検索対象が見つからないと例外発生するが、find_byではnilを返す
```ruby
irb(main):010:0> Book.find(10)
  Book Load (0.5ms)  SELECT "books".* FROM "books" WHERE "books"."id" = ? LIMIT ?  [["id", 10], ["LIMIT", 1]]
/Users/hatajunnosuke/.rbenv/versions/3.0.3/lib/ruby/gems/3.0.0/gems/activerecord-7.0.2.2/lib/active_record/core.rb:284:in `find': Couldn't find Book with 'id'=10 (ActiveRecord::RecordNotFound)
irb(main):011:0> B
irb(main):011:0* Book.
irb(main):011:0> Book.find_by(price:10)
  Book Load (0.5ms)  SELECT "books".* FROM "books" WHERE "books"."price" = ? LIMIT ?  [["price", 10], ["LIMIT", 1]]
=> nil               
```
- 複数件のレコードを検索する（where）
```ruby
irb(main):012:0> Book.where("price > ?",3000)
  Book Load (1.3ms)  SELECT "books".* FROM "books" WHERE (price > 3000)
=>                                                              
[#<Book:0x00007fd7b7ee3450                                      
  id: 4,                                                        
  name: "Book 4",                                               
  published_on: Sat, 24 Aug 2019,                               
  price: 4000,                                                  
  created_at: Wed, 23 Feb 2022 01:31:16.987211000 UTC +00:00,   
  updated_at: Wed, 23 Feb 2022 01:31:16.987211000 UTC +00:00>,  
 #<Book:0x00007fd7b7ee3388                                      
  id: 5,                                                        
  name: "Book 5",                                               
  published_on: Wed, 24 Jul 2019,                               
  price: 5000,
  created_at: Wed, 23 Feb 2022 01:31:16.988484000 UTC +00:00,
  updated_at: Wed, 23 Feb 2022 01:31:16.988484000 UTC +00:00>]
```
条件に該当しない場合は空のActiveRecord::Relationインスタンスを返す。

![スクリーン ショット 2023-05-02 に 21 16 24 午後](https://user-images.githubusercontent.com/81806676/235663714-d4275893-3d63-47c0-a4f7-4df9eb88335a.png)

### scopeを定義
scopeはよく利用する検索条件に名前をつけてひとまとめにしたもの。
```ruby
class Book < ApplicationRecord
  scope :costly,->{where("price >?",3000)}
  # 「Javaについて書かれた」という検索をする場合
  scope :written_about,->(theme){where("name like ?","%#{theme}%")}
end
```
- Scopeのメリット
  - 繰り返し利用するクエリの再利用性が上がる
  - クエリに名前をつけることで、可読性が向上する

- default_scopeで常にスコープをかけることができるが、利用性が損なわれる可能性があるので使い方に注意。
## モデル同士のリレーション
`$ bin/rails g migration AddPublisherIdToBooks publisher:references`
- referencesという型は別のモデルへの参照を意味する。
- 本の通りにやることでbooksに「publisher_id」カラムが追加されて、not null制約、外部キー制約が追加された。
- BookとPublisherの関係を「1対多」と呼ぶ。
- ActiveRecordでこれを表現するには`has_many`　`belongs_to`を追加する。これによりPublishモデルにbooksというメソッドが、Bookモデルにはpublisherメソッドが定義される。これでお互いの情報を引き出したり、できる。
- 「1対1」の関係には「has_one」というクラスメソッドを利用する。
 
### 多対多のリレーションを実現する　
ActiveRecordで多対多を表現するには**中間モデル**を作成することになる。
例では著者は複数の本を書くことに加えて、本は共著などで複数の著者が存在することを考慮すると「多対多」の関係性になる。０９
1. BookAuthorモデルを作る
2. booksとauthorsモデルにhas_manyのオプションthroughで中間テーブルを指定する。


## モデルを通じてデータを更新する
### バリデーションと「！」付きメソッド
- 保存せずにバリデーションの確認をしたい場合はvalid?メソッドを使うといい。
- 「！」ありはバリデーション失敗時に例外「ActiveRecord::RecordInvalid」が起こる

## コールバック
- ActiveRecordではレコードを作成して保存する一連の流れの間のさまざまな箇所で任意の処理を差し込める。（コールバック）
- 前処理、後処理などを宣言的にかける
- 必ず保存後に行いたい処理がある時に実行もれを防ぐことができる
- :ifまたは:unlessというオプションによって特定の条件を満たした場合のみ実行するコールバックを実装できる（ただし、delete,update_*では指定できない）

## ActiveRecord::Enumで列挙型を扱う
バリエーションが少ないならば数値で管理したほうが効率的。1なら販売中、2なら売り切れみたいに。Enum型はカラムに対してプログラム上別名を与えることができる。（modelに定義する）
```ruby
enum sales_status: {
    reservation: 0,
    now_on_sale: 1,
    end_of_print: 2
  }
#コンソールで使うと  
irb(main):058:1* Book.create(
irb(main):059:1*   name:"enum Book",
irb(main):060:1*   sales_status: 1,
irb(main):061:1*   publisher: Publisher.find(1),
irb(main):062:1*   price: 100
irb(main):063:0> )
  Publisher Load (1.0ms)  SELECT "publishers".* FROM "publishers" WHERE "publishers"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  TRANSACTION (0.1ms)  begin transaction
  Book Create (0.8ms)  INSERT INTO "books" ("name", "published_on", "price", "created_at", "updated_at", "publisher_id", "sales_status") VALUES (?, ?, ?, ?, ?, ?, ?)  [["name", "enum Book"], ["published_on", nil], ["price", 100], ["created_at", "2022-02-23 03:36:35.715778"], ["updated_at", "2022-02-23 03:36:35.715778"], ["publisher_id", 1], ["sales_status", 1]]
  TRANSACTION (0.8ms)  commit transaction
=> 
#<Book:0x00007fdda6645d08
 id: 1,
 name: "enum Book",
 published_on: nil,
 price: 100,
 created_at: Wed, 23 Feb 2022 03:36:35.715778000 UTC +00:00,
 updated_at: Wed, 23 Feb 2022 03:36:35.715778000 UTC +00:00,
 publisher_id: 1,
 sales_status: "now_on_sale"> #1と入力したのにnow_on_saleと表記されている！！
```

## コントローラーの役割
### ブラウザからアプリケーションにアクセスしたときの流れ
- コントローラーはURLのパスとルーティングから導かれたアクションの中で、モデルなどを通じて必要なデータを取得・加工する。
- 最後にそのデータをビューに渡してレンダリングすることでWEBアプリケーションの画面をユーザーに返す役割を担う。

### アクションに対してフックで処理を差し込む
- フック・・アクションの前後に処理を差し込むことのできるコールバック処理のようなもの。フィルターと呼ぶこともある。
```
class BooksController < ApplicationController
  protect_from_forgery except: [:destroy]
  before_action :set_book,only:[:show,:destroy] <=これがフック　（これにより共通の処理を定義できてまとめられる）
  
  def show
   
    respond_to do |format|
      format.html
      fotmat.json
    end
  end
  
  def destroy
    
    @book.destroy
    respond_to do |format|
      format.html { redirect_to "/"}
      fotmat.json {head :no_content}
    end
  end
  
  private
  def set_book
    @book = params[:id]
  end
end
```
- フックはonlyやexceptオプションで特定のアクションに対してのみ実行できる。
- フックの一例  
 [![Image from Gyazo](https://i.gyazo.com/76cc6a3641bac2a0ebc38f88a509c193.png)](https://gyazo.com/76cc6a3641bac2a0ebc38f88a509c193)
 - aroundフックは呼び出すメソッドではbeforeの後yieldを使ってアクション側に処理を戻す必要がある。
　　- フックのスキップ
  - フックは`skip_before_action`などのようにスキップすることもできる。

### ルーティングとリソース
- CRUD操作は`resouces :publishers`の一行で書き、定義できる。
- resoucesのブロックの中にresourcesを書くことで拡張することが可能
- memberを利用すると「/publishers/:id/detail」のように個別のリソースに対してアクションを追加できる。
- collectionを利用すると「/publishers/search」のように全体のリソースに対してアクションを追加できる。
- 反対にonlyでルーティングを制限することもできる。
- ログインユーザーの自身のプロフィールのように1人のユーザーから見てアプリケーション上に1つしか存在しないリソースは`resource`で定義する。（こちらではindexが作成されない,idと紐づかない）
```ruby
# route.rb
  resources :publishers
  resource :profile, only: %i{show edit update}

# bin/rails routes
publishers     GET    /publishers(.:format)                                                                          publishers#index
               POST   /publishers(.:format)                                                                          publishers#create
new_publisher  GET    /publishers/new(.:format)                                                                      publishers#new
edit_publisher GET    /publishers/:id/edit(.:format)                                                                 publishers#edit
publisher      GET    /publishers/:id(.:format)                                                                      publishers#show
               PATCH  /publishers/:id(.:format)                                                                      publishers#update
               PUT    /publishers/:id(.:format)                                                                      publishers#update
               DELETE /publishers/:id(.:format)                                                                      publishers#destroy

edit_profile GET    /profile/edit(.:format)                                                                          profiles#edit
  profile    GET    /profile(.:format)                                                                               profiles#show
             PATCH  /profile(.:format)                                                                               profiles#update
             PUT    /profile(.:format)                                                                               profiles#update

```

### 例外処理
- 一般的な例外が発生した場合は500のステータスコードを返す
- Railsで特別扱いされる例外
[![Image from Gyazo](https://i.gyazo.com/95c274d7780cbde2e29e0e6c933047c9.png)](https://gyazo.com/95c274d7780cbde2e29e0e6c933047c9)
- 特定の例外に対して挙動を指定したい場合`rescue_from`を使うことで実装できる。

### Storong Paramater
```
def update
 略
 user.update(user_params)
end

private
 def user_params
  params.require(:user).permit(:name, :email)
 end
end
```
user_paramsの中ではparamsからモデルの更新に利用して良いパラメータを明示している。
- リクエストには:userというキーが必要
- userの中で受け付けて良いのは「:name, :email」の2つのみ

## コントローラとビューの強調とビューテンプレートの基本
## レンダリングまでの流れ
- ビューはコントローラで取得したデータをユーザが認識可能な形式にレンダリングするところで、例えばHTMLによる表示であったり、APIの返り値であったりする。
- レンダリングの方法としては一番簡単なのがコントローラ内でrenderメソッドを利用すること。
```
def show
 render :show #ここで描画するためのテンプレートを探す・見つかったテンプレートをもとにデータを展開して最終的なHTMLを作成する
end
```
- render :showを省略可能。その場合、暗黙的にrender :アクション名と判断される。
- RailsではHTMLだけではなく、JSONやXMLなどその他のフォーマットで表示させることも可能。
```
def show
 respond_to do |format|
  format.html
  format.json{ render json: @book}
 end
```
これで`books/1.json`でアクセスするとJSONデータが描画される。

## 部分テンプレートとLayout
### 部分テンプレート
- renderで部分テンプレート（パーシャル）の読み込みが可能
- 「_00」と定義して、render"00"のように記述すると、ファイル名が_00ではじまるテンプレートファイルを探す。

### Layoutテンプレート
- 全体のテンプレートを設定するなら/layout/application.html.erbへ
### variantsによるテンプレートの切り替え
- variants機能を使えば、PC、モバイルなどの端末に対応したものを作れる。

## ビューテンプレート
### ヘルパーメソッド
- url_for
- link_to
- form_with
- このほか独自で定義することもできる

### エスケープ処理
クロスサイトスクリプティング（XSS）・・プログラムが意図しないような文字列を渡すことであるページに対して自由にJavaScriptを埋め込んで実行させてしまう攻撃方法
railsにはXSSに対する対策がフレームワークとして組み込まれている。

### テンプレートエンジン
- ERB
- Haml
- Slim

### APIサーバにとってのビューについて
省略(Reactとか使う場面になったら読めばいいかな)

## まとめ
**アプリケーションの主要なロジックはなるべくモデルに書くべき**
- メリット コードが整理される、テストがしやすいなど
- 肥大化したモデル「Fat Model」と呼ぶ。
