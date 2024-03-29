# 3章　押さえておきたいRailsの基本機能
## テストの種類と実行方法
テストの役割分担
![スクリーン ショット 2023-05-04 に 08 12 07 午前](https://user-images.githubusercontent.com/81806676/236071024-0d616dd3-1d51-4d0e-9e51-480290a97fcf.png)
## テストの種類と実行方法
基本的には本で確認せよ
- テストの実行方法
  - `$ bin/rails test`
  - `$ bin/rails test test/models`#<-ディレクトリ指定
  - `$ bin/rails test test/models/todo_test.rb -n test_the_truth`#<-　-n(--name)でテスト名も指定して実行できる。  

-  fixture
7章でfactory_botの解説がある
### Rails 6.0で追加された並列テスト
- Rails 6.0からテストを高速で実行するため（デフォルトで）並列実行が行えるようになった。
- 並列の指定はtest/test_helper.rbでできる。

## RackとRailsの関係
- RackはWebアプリケーションサーバとWebアプリケーションフレームワーク間のインターフェイスを共通化した仕様であり実装となっているライブラリ。
- RackのおかげでUnicornやPumaといったアプリケーションサーバとRailsなどのフレームワーク間のスムーズなやりとりが行えるようになっている。
- RailsもRackの使用に則ったアプリケーション
```
ここは省略（後日）というよりrackについて気になったらこの章を読んでくれ。
```
## DBを管理する
[![Image from Gyazo](https://i.gyazo.com/a418a6e095668f19f46e115d9d6d5b81.png)](https://gyazo.com/a418a6e095668f19f46e115d9d6d5b81)
- create,dropはdatabase.ymlに定義されている値を使って作成、削除する。RAILS_ENVを指定しなければdevelopmentとtest環境のスキーマを作成する。
- migrateは生成されているマイグレーションファイルをもとにDBへテーブル定義を反映させる。`db:migrate:status`を使うと反映済みの状態を確認できる。
- マイグレーションファイル内で利用できる専用メソッドはup,down,change
- 一度実行したmigrateを取り消したい場合rollbackを使う。 STEP=nで巻き戻す個数を指定できる。
- schema.rbではDBの構成を残している。schemaファイルを読み込むことでマイグレーションを実行することなくDB環境を作成できる。（db:setup）
- 明示的にスキーマファイルを生成したい場合はdb:schema:dump、読み込む際はdb:schema:loadを使う。
- db:seedを実行するとseeds.rbのデータを読み込む。データ投入の際は！でエラー発生時に気づけるようにするのが良い。なお、db:seed:replantで一度レコードが削除され、改めてdb:seedを実行される。
- db:resetはdrop,setupを同時に行う。
- Rails6.0からは複数DBへ対応した。使い方として、書き込み用と読み取り用に分けて負荷を軽減することができる。→パフォーマンスを上げたいときに活用できるかも。「「

## 秘密情報を管理する
```
ここも気になれば読んでくらいで良いかと。
```
## HTTPとRailsアプリケーション
### 通常のHTTPリクエスト
[![Image from Gyazo](https://i.gyazo.com/d38a05a2653145500ac9a158e261c916.png)](https://gyazo.com/d38a05a2653145500ac9a158e261c916)
1. ブラウザがHTMLをリクエストする
2. サーバがリクエストを受けつけ、DBに問い合わせを行うなどしてHTMLを生成する。
3. ブラウザがHTMLを受け取る
4. HTMLのパースを開始
5. JavaScriptやCSS、フォントや画像などのアセットをリクエスト
6. ブラウザがアセットを受け取る
7. アセットのパース
8. HTMLのレンダリングが完了する
Early Hintsを利用するとページのロード速度を向上できる可能性がある。
Early Hintsに対応したブラウザが少ないことやセキュリティー上の問題があることからリバースプロキシを介して使うことが現実的。

### アセットのロード開始タイミングを早める
```
ここは省略（後日）
```

### Content Security Policy(CSP)への対応
- コンテンツセキュリティーポリシー（CSP）は、主にクロスサイトスクリプション（XSS）の影響を軽減することを目的とした仕組み
