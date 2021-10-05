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

