# 2章　Ruby on RailsとMVC
## MVCアーキテクチャ
- M・・Model データベースとの接続とデータに対する操作、及びビジネスロック
- V・・View Modelの内容を参照し視覚表現を行う部分
- C・・Controller Modelのロジックを呼び出し、必要なViewの選択など、ModelとViewをつなぐ部分

## ActiveRecordでモデルを実装する
`$ bin/rails g model Book`
これで作成されるマイグレーションファイルの中身はテーブルの定義をActiveRecord：：MigrationのDSLで記述されたもの。
### ActiveRecordによるモデルの役割
1. データベースと接続し、データベースのレコードとActiveRecordオブジェクトを結びつける役割（これによりモデルクラスに記述がなくても自動で反映する機能はSQLの抽象化、接続情報の隠蔽につながる）
2. ビジネスロックの実装的な振る舞いに関するところ、すなわちバリデーションやレコード保存時などに実行するさまざまなコールバックなどを実行する役割

### scopeを定義
scopeはよく利用する検索条件に名前をつけてひとまとめにしたもの。
```
class Book < ApplicationRecord
  scope :costly,->{where("price >?",3000)}
  scope :written_about,->(theme){where("name like ?","%#{theme}%")}
end
```
```
学習メモ
ActiveRecord：：Relationの理解が不十分
```

## モデル同士のリレーション
`$ bin/rails g migration AddPublisherIdToBooks publisher:references`
- referencesという型は別のモデルへの参照を意味する。
- 本の通りにやることでbooksに「publisher_id」カラムが追加されて、not null制約、外部キー制約が追加された。
- BookとPublisherの関係を「1対多」と呼ぶ。
- ActiveRecordでこれを表現するには`has_many`　`belongs_to`を追加する。これによりPublishモデルにbooksというメソッドが、Bookモデルにはpublisherメソッドが定義される。これでお互いの情報を引き出したり、できる。
- 「1対1」の関係には「has_one」というクラスメソッドを利用する。
 
## 多対多のリレーションを実現する
ActiveRecordで多対多を表現するには**中間モデル**を作成することになる。
1. BookAuthorモデルを作る
2. 各モデルにhas_manyのオプションthroughで中間テーブルを指定する。

```
学習メモ
中間テーブルについて不十分
```

## モデルを通じてデータを更新する
### バリデーションと「！」付きメソッド
- 「！」ありはバリデーション失敗時に例外「ActiveRecord::RecordInvalid」が起こる

## コールバック
- ActiveRecordではレコードを作成して保存する一連の流れの間のさまざまな箇所で任意の処理を差し込める。（コールバック）
- 前処理、後処理などを宣言的にかける
- 必ず保存後に行いたい処理がある時に実行もれを防ぐことができる
- :ifまたは:unlessというオプションによって特定の条件を満たした場合のみ実行するコールバックを実装できる（ただし、delete,update_*では指定できない）

## ActiveRecord::Enumで列挙型を扱う
バリエーションが少ないならば数値で管理したほうが効率的。1なら販売中、2なら売り切れみたいに。Enum型はカラムに対してプログラム上別名を与えることができる。（modelに定義する）

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
 - フックは`skip_before_action`などのようにスキップすることもできる。

### ルーティングとリソース
- CRUD操作は`resouces :publishers`の一行で書き、定義できる。
- resoucesのブロックの中にresourcesを書くことで拡張することが可能
- 反対にonlyでルーティングを制限することもできる。
- ログインユーザーの自身のプロフィールのように1人のユーザーから見てアプリケーション上に1つしか存在しないリソースは`resource`で定義する。（こちらではindexが作成されない,idと紐づかない）

### 例外処理
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
省略

## まとめ
**アプリケーションの主要なロジックはなるべくモデルに書くべき**
- メリット コードが整理される、テストがしやすいなど
- 肥大化したモデル「Fat Model」と呼ぶ。
