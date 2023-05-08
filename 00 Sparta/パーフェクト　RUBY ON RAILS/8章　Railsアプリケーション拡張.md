## Active Storageを利用するための下準備
- モデルにh`as_one_attached :image`を追加することでimageカラムを作成できる
## 既存のフォームに画像アップロード機能を追加する
- チェックボックスの:remove_imageにもストロングパロメーターを入れる
- 画像の表示にはimage_tagを使用する。
- 単に画像を削除するために
  1. remove_imageをattr_accessorで明示的に属性を宣言する（event.rb）
  2. before_saveコールバックでイベントを保存する前にremove_imageがtrueの時imageをnilにすることで保存時に画像を削除するようにするメソッドを記述

- 誤って削除して元に戻したいケースを想定してdependent: falseを追加（ここはイマイチの理解）

### サムネイルを生成する
cssで画像のheightやwidthを指定して表示することでレイアウトを調整できそうだが、転送量を考慮するとおすすめできない。
なのでサムネイル生成機能を使う。
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
- バリデーションを追加後、イベント編集時に画像がバリデーションエラーになると「モデルに画像はアサインされているけど画像のアップロードは完了していない」という中途半端な状況になる。
  - そこで解決のためにif @event.image.attached?&& @event.image.blob&.persisted?とすることで画像が実際にアップロードされたかまで判別できるようになる。
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
