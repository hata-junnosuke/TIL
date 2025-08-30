# Bulletの使い方 --Rails開発でN+1問題を指摘してくれるgem
# はじめに

Rails開発において、パフォーマンスの問題で最も頻繁に遭遇するのが「N+1問題」です。開発環境では気づきにくいこの問題は、本番環境で深刻なパフォーマンス低下を引き起こす可能性があります。

本記事では、N+1問題を自動的に検出してくれる強力なgem「Bullet」について、基本的な使い方を解説します。

# 何ができるの？

## Bulletの主要機能

Bulletは、Railsアプリケーションにおいて以下の問題を検出・通知してくれます。

### 1. N+1クエリの検出
```ruby
# N+1問題が発生するコード例
users = User.all
users.each do |user|
  puts user.posts.count  # ユーザー数分のクエリが発生
end
```

### 2. 使用されていないeager loadingの検出
```ruby
# postsを読み込んでいるが使用していない
users = User.includes(:posts)
users.each do |user|
  puts user.name  # postsは使用されていない
end
```

## どうやって通知してくれるの？

Bulletは検出した問題を様々な方法で教えてくれます。
- ブラウザのJavaScriptアラート
    ![スクリーン ショット 2025-08-29 に 20.31.45 午後.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/cdb4f51f-c78d-4967-8fbe-c7ba44ddbdbd.png)

- ブラウザのコンソールログ
    ![スクリーン ショット 2025-08-29 に 20.30.14 午後.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/cb470d8b-dad1-4526-8019-98064376b17a.png)

- Railsログ
    ```
     user: root
     GET /api/v1/test_n_plus_one/unused_eager_loading
     AVOID eager loading detected
       User => [:running_records, :monthly_goals]
       Remove from your query: .includes([:running_records, :monthly_goals])
     Call stack
    /myapp/app/controllers/api/v1/test_n_plus_one_controller.rb:60:in 'Enumerable#map'
    /myapp/app/controllers/api/v1/test_n_plus_one_controller.rb:60:in 'Api::V1::TestNPlusOneController#unused_eager_loading'
    ```
他にも以下の形式で通知ができるようです。

- Slack通知
- メール通知


詳しくは[Bullet 公式リポジトリ](https://github.com/flyerhzm/bullet)でご確認ください🙇

# 導入

## インストール

Gemfileに以下を追加：

```ruby
group :development do
  gem 'bullet'
end
```

その後、bundleコマンドを実行：

```bash
bundle install
```


## 基本設定
インストールしたら以下のコマンドを実行：
```bash
bundle exec rails g bullet:install
```
`config/environments/development.rb`に以下の設定が追加されます。

```ruby
Rails.application.configure do
  config.after_initialize do
    Bullet.enable = true
    Bullet.alert = true         # ブラウザのJavaScriptアラート
    Bullet.bullet_logger = true # Bulletのログファイル(log/bullet.log)
    Bullet.console = true       # ブラウザのコンソールログ
    Bullet.rails_logger = true  # Railsのログ
    Bullet.add_footer = true    # ページ下部に通知を表示
  end
end
```

# 使用方法

## 基本的な使い方

### N+1問題の検出と解決
N+1問題を発生するコードが実行されるときに、先に示した方法で警告が表示されます。
警告の中に解決方法が示されているので、その指示に従うと解決できます。

```ruby
# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  def index
    # N+1問題が発生するコード
    @posts = Post.all
    
    # Bulletが警告を出す
    # USE eager loading detected
    #   Post => [:user]
    #   Add to your query: .includes([:user])
  end
end
```

```erb
<!-- app/views/posts/index.html.erb -->
<% @posts.each do |post| %>
  <h3><%= post.title %></h3>
  <p>著者: <%= post.user.name %></p>  <!-- ここでN+1が発生 -->
<% end %>
```


解決方法：

```ruby
class PostsController < ApplicationController
  def index
    # includesを使用してN+1を解決
    @posts = Post.includes(:user)
  end
end
```

# まとめ
**開発初期からの導入をオススメします！**
- プロジェクト開始時からBulletを導入することで、N+1問題の蓄積を防ぐことができます。

**注意点**
- すべての警告に対応する必要はないと思います。使ってみるとノイズに感じる場面もありました。そんなときは[セーフリスト](https://github.com/flyerhzm/bullet?tab=readme-ov-file#safe-list)に設定することもできるのでそちらも検討してみてください！

Bulletは、Rails開発において必須とも言えるツールです。適切に設定・運用することで、アプリケーションのパフォーマンスを大幅に改善できます。ぜひ導入して、快適なRails開発を実現してください！

# 参考リンク

- [Bullet 公式リポジトリ](https://github.com/flyerhzm/bullet)
- [Rails Performance 最適化ガイド](https://guides.rubyonrails.org/v7.0/active_record_querying.html#eager-loading-associations)
- [N+1 Queries or Memory？](https://www.speedshop.co/2020/05/11/the-rails-performance-workshop-is-live.html)
