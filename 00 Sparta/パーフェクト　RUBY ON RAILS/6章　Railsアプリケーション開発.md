この章は開発を実施するので基本的には本で確認することにして、ここではメモを残しておくことにする。
## Hamlの導入
1. gemの追加 `hamlit-rails` `html2haml`
2. `$ bundle`
3. `$ bin/rails hamlit:erb2haml`
4. `html2haml`を消す（テンプレートエンジンがHamlとHamlitが存在して意図しない動きをする可能性を排除するため）

### bootstrap
フロントエンドフレームワークでjQuery,popper.jsに依存関係がある

## OAuthを利用して「GitHubでログイン」機能を作る
### OAuthとは
第三者となるアプリケーションに対して安全にアクセス権限を提供するためのプロトコル

### ログアウト
```ruby
class ApplicationController < ActionController::Base
  helper_method :logged_in? #helper_methodでどこでも使えるようにする。

  private

  def logged_in?
    !!session[:user_id]　# ！！はnot演算子でnillならfalseを返す、それ以外はtrue
  end
end
```
