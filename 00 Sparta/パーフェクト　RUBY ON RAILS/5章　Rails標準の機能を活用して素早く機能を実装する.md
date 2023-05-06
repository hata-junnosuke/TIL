# Active Jobによる非同期実行
- Active Jobは非同期実行処理機能を提供するライブラリ。バックエンド上で実行する非同期処理を統一的に利用できる。
- 非同期処理はメール送信やデータを集計してCSVファイルを作るなどの時間がかかる処理で利用される。
- 長い時間かかる処理を非同期で別途実行することでユーザへの応答を早くできる。
## ジョブの実装
読んで実行すればわかる。
```ruby
# Job実行
irb(main):001:0> AsyncLogJob.perform_later
Enqueued AsyncLogJob (Job ID: 7453ef98-5b0d-47f6-b848-b56a2b4745eb) to Async(default)
=> 
#<AsyncLogJob:0x00007f9c8b086658
 @arguments=[],
 〜〜
Performed AsyncLogJob (Job ID: 7453ef98-5b0d-47f6-b848-b56a2b4745eb) from Async(default) in 41.24ms

#　　jobの確認
irb(main):002:0> AsyncLog.last
  AsyncLog Load (1.4ms)  SELECT "async_logs".* FROM "async_logs" ORDER BY "async_logs"."id" DESC LIMIT ?  [["LIMIT", 1]]
=> #<AsyncLog:0x00007f9c8f98a070 id: 1, message: "hello", created_at: Sat, 26 Feb 2022 05:25:34.706134000 UTC +00:00, updated_at: Sat, 26 Feb 2022 05:25:34.706134000 UTC +00:00>

#引数をつけて実行
irb(main):003:0> AsyncLogJob.perform_later(message: "hi")
Enqueued AsyncLogJob (Job ID: 84b2b944-34a2-40d4-8290-1a3f246f963b) to Async(default) with arguments: {:message=>"hi"}
〜〜
Performing AsyncLogJob (Job ID: 84b2b944-34a2-40d4-8290-1a3f246f963b) from Async(default) enqueued at 2022-02-26T05:26:55Z with arguments: {:message=>"hi"}
   (0.1ms)  SELECT sqlite_version(*)
                                      T
# 1分遅れで実行する(setメソッド)
irb(main):005:0> AsyncLogJob.set(wait: 1.minute).perform_later(message: "hi")
Enqueued AsyncLogJob (Job ID: 18a55fd8-797d-40be-b01f-6306bed5333b) to Async(default) at 2022-02-26 05:28:53 UTC with arguments: {:message=>"hi"}
=> 
#<AsyncLogJob:0x00007f9c8f9f0208
```
### バックエンドの設定
Active JobはバックエンドとつなぐアダプターとしてSidekiqなどがある。ActiveJobで非同期処理を実装しておくとバックエンドを設定で切り替えることができる。
本の通りにやると非同期処理を実装できる。
- sidekiq
  - redisが必要
  - sidekiqにはキューの状態を確認するWeb UIが用意されている。
   - ルーティングの加筆が必要。またproduction環境ではアクセス制限をかけること。 

### 複数のキューの管理
- hoge.job.rbにqueue_as :hogeとすることでキュー名をつけれて指定ができるようになる。
- その際sidekiq.ymlにも読み込むようにキュー名を入れておく必要がある。

### ジョブの例外処理
ActiveJobには例外をキャッチして対応を変える仕組みがある
- retry_on・・ジョブをリトライ
- descard_on・・ジョブを破棄する

### ジョブのテスト
読んでくれ。

## Active Storageによるファイルアップデート
### Active Storageを利用したサンプルアプリ作成
本通り。
- `$ bin/rails active_storage:install`でインストールして
- `$ bin/rails g scaffold user name portrait:attachment`のようにすると、has_one_attachedとされる
- なおportraitはカラムに含まれていなくて別テーブルで管理されている。

 ### Active Storageの動作と設定
 - Action Storageの機能は2つのモデルで作られる。
 - 他の画像アップロード用のgemではモデルにカラムを持たせることが多いが、ActionStorageではポリモーフィック関連を選択している。
  - ActionStorage::Attachment・・モデルとBlobの中間テーブル
  - ActionStorage::Blob・・アップロードファイルのメタ情報を管理するモデル
  ![スクリーン ショット 2023-05-06 に 09 07 10 午前](https://user-images.githubusercontent.com/81806676/236587244-3130a337-f819-4502-aa3a-40eaf1275a11.png)

- 実際の本番環境ではS3に保存することが多い。config/storage.ymlに定義することで設定できる。

### サムネイルの生成
- サムネイルの生成タイミング
 - CarrierWave→アップロードしたタイミングでサムネイルを生成
 - Shrine→アップロードした後、非同期でサムネイルを生成可能
 - Active Storageはサムネイル画像用のURLにアクセスしたタイミングでサムネイルを生成 
- サムネイルの生成にはImageProcessingとImageMagicが必要

### ファイルへのアクセス制限
期限付きのURLを生成して仮にファイルのURLが公開されても被害を限定的に抑えることができる。
詳細は読んで

### ダイレクトアップロード機能
アプリケーションサーバーを経由しないことで時間を減らしサーバーへの負荷も減らすことができる。
derect_upload: trueを追加することでフォームのサブミットボタンを押したタイミングでファイルをストレージへダイレクトアップロードするようになる。
複数同時にやりたい場合はmultiple: trueオプションをつけると可能

### ActiveStorageの欠点
- validationヘルパーの不足
- cacheが不足（バリデーションに引っ掛かったら、再度画像を選ばなくてはならない。）
- 今後に期待

## Action Mailerによるメール送信
- Action Mailer・・メール送信機能
- Aciton Mailbox・・メール受信機能

### Action Mailerの使い方
- `$ bin/rails g mailer ~~`でファイルを作成して
- 〜.rbでアクション名と内容を記述して
- コントローラーで`UserMailer.with(to:"hoge@example.com", name: "fuga").welcome.deliver_now`のようにして呼び出す。
- 送信元はapplication_mailer.rbに記載されているので自分のアプリケーション用に編集すれば良い。

### Action Mailboxによるメール受信
大まかな流れ
- Sendgridにメールが届く
- Sendgridが届いたメールデータを添えてRailsアプリの特定URLへリクエストを投げる 
- Action Mailboxがメールデータを保存したり、メールに応じて非同期処理を行う

`Mailboxについてはまたやろう`

## Action Textによるリッチテキスト機能
- Rails6.0で追加
- リッチテキストを編集するためのエディタと保存するデータベースを簡単に用意できる

### Action Textとは
機能
- WYSIWYGエディタ（リッチテキストを編集するエディタ）
- リッチテキストコンテンツを保存するモデル
- リッチテキストを取り扱うヘルパーメソッド群

### Action Textの使い方
本の通りに用意

### Action Textの中身と利用時の注意点
- リッチテキストはActionText：：RichTextという別のモデルで管理されている
- なのでN＋1問題には注意
- 専用のeagerload
 - with_rich_text_#{name}
 - with_rich_text_#{name}_and_embeds

## Action Cableによるリアルタイム通信
- WebSocketを使ったリアルタイム処理を提供するライブラリ。
- WebSocketはクライアントとサーバー間のコネクションを維持し双方向でデータをやりとりする通信規格

　### Action Cable
 `作ってはみたが使うときに再読するくらいでいいかと。`

