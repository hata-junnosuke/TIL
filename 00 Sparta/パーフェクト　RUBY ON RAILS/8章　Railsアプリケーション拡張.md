## Active Storageを利用するための下準備
- モデルにh`as_one_attached :image`を追加することでimageカラムを作成できる
## 既存のフォームに画像アップロード機能を追加する
- チェックボックスの:remove_imageにもストロングパロメーターを入れる
- 画像の表示にはimage_tagを使用する。

### サムネイルを生成する
- `gem image_processing`(画像変換用のツールのラッパーとなるライブラリ)のコメントアウトを外す
- variant(resize_to_fit: [200,200]としたことで該当するコードを実行したタイミングでリサイズした画像のUrlを返すようになる。初めてアクセスしたタイミングで生成され2回目以降サムネイルは生成されない。

### 画像以外のファイルをアップロードした場合にバリデーションエラーにする
- gem 'active_storage_validations'をインストール
- あとはvalidatesをつける。
```ruby
validates :image,
            content_type: [:png, :jpg, :jpeg],
            size: {less_than_or_equal_to: 10.megabytes},
            dimension: {width: {max: 2000},height: {max: 2000}} 
```
## gemで機能拡張をする
### Kaminariページネーション機能を作る
ここはOK
### Searchkickでイベント検索機能を作る
ここは再度やる
うまくいかなかった。
## 落穂ひろい
ここは復習が必要
### エラーハンドリング
- Railsのエラーハンドリング方法
  - コントローラーでrescue_fromメソッド
  - エラーハンドリング用のRack Middleware

#### rescue_fromを利用したエラーハンドリング
本で見て
- こちらは簡単だがRack Middlewareで発生したエラーを検知できないという問題がある

### ルーティングの制限
余計なルーティングはないほうがいい。onlyで制御すべき。
