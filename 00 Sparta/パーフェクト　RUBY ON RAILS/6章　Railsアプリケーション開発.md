この章は開発を実施するので基本的には本で確認することにして、ここではメモを残しておくことにする。
## Hamlの導入
1. gemの追加 `hamlit-rails` `html2haml`
2. `$ bundle`
3. `$ bin/rails hamlit:erb2haml`
4. `html2haml`を消す（テンプレートエンジンがHamlとHamlitが存在して意図しない動きをする可能性を排除するため）

## bootstrap
フロントエンドフレームワークでjQuery,popper.jsに依存関係がある

## OAuthを利用して「GitHubでログイン」機能を作る
### OAuthとは
第三者となるアプリケーションに対して安全にアクセス権限を提供するためのプロトコル
- OAuthを利用すると運営者にGitHubのパスワードを渡さずに必要な権限のみを与えることができる
- 「Twitterでログイン」などはこの技術

### OmniAuthとは
- OmniAuthとはユーザ認証機能を提供するベースとなるgem。
- RailsでOmniAuthを使うときはクロスサイトリクエストフォージェリ（CSRF）の脆弱性への対策が必要（gem omniauth-rails_csrf_protectionを使う）
- 本番環境では注意が必要（環境変数について勉強が必要）

### ログイン処理を作成する
- CSRF対策のためにリンクはpostメソッドでやる
- /auth/githubでGitHubのアプリケーション認証画面に遷移する。
- OmniAuthはRackミドルウェアとして動作して「/auth/:provider」というURLのルールにマッチするアクセスを受け取ると認証処理を開始する（：providerは認証するサービスプロバイダーを指定する）
- sessions#createは本で確認せよ。
- /auth/:provider/callbackがコールバック用のURLとデフォルトでなっているのでsessions#createと結びつける

### ログアウト
- helper_methodでログインを確かめるメソッドを定義する。
```ruby
class ApplicationController < ActionController::Base
  helper_method :logged_in? #helper_methodでどこでも使えるようにする。

  private

  def logged_in?
    !!session[:user_id]　# ！！はnot演算子でfalseかnillならfalseを返す、それ以外はtrue
  end
end
```
- destroyの中でreset_sessionをすることで値を削除することができる
- destroyアクションを/logoutに結びつける

## イベントの登録機能を作る
### イベント用のモデルを作成する
- モデル作成の際に`rails g resource`コマンドで作成するとroutes.rb内にresources :eventが既に追加することができる。 
- validatesとvalidateの違い（調べろ）
### UserモデルとEventモデルの関連づけ
```ruby
class User 
  has_many :created_events, class_name: "Event", foreign_key: "owner_id"
略
```
- 単純にEventモデルを関連づけるのではなく、識別のため「created_events」で関連づける
- さらに外部キーはデフォルトのuser_idではなくowner_idで設定している

### バリデーションエラーの設定
- form_withでAjaxの挙動をオンにしたい場合はlocal: falseというオプションをつける必要がある（Rails6．1以降）これにより、data-remote="true"の属性が追加されるようになる。
- data-remote="true"の属性を持つformが送信されるとrails-ujsがそれを捕捉してAjaxで非同期にフォーム情報を送信する。

## イベントの閲覧機能を作る
### イベント詳細ページの作成
- 詳細ページの中で時間を表示する場面ではlメソッドを使っている。（i18nのメソッド）

## 登録されたイベントへの参加機能、参加キャンセル機能を作る
### イベント参加機能の作成
- モデルの作成の際、マイグレーションファイルで`add_index :tickets, %i[event_id user_id], unique: true`とすることでユーザがイベントに重複して参加することがないようにしている

## 退会機能を作る
### 退会機能の作成
- has_manyにdependent: :nullifyオプションを追加することで削除した時に関連するレコードの外部キーをnullにできます。
