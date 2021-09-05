# ポートフォリオにRSpecを導入してテストを1つ通すまで。
## gemのインストール
公式からインストール。ポートフォリオではRail5系なのでRSpec4.1.2をインストール。
## RSpecをインストール
- `$ bin/rails db:create:all`→もうあるわ。  
- `$ rails generate rspec:install`→ファイルが作られた。
[![Image from Gyazo](https://i.gyazo.com/7ba43d0b7813fea0c480271cd2a69a63.png)](https://gyazo.com/7ba43d0b7813fea0c480271cd2a69a63)
- `$ bundle exec spring binstub rspec`でbinディレクトリにrspecの実行ファイルを作成。
- `$ bin/rspec`でテスト実行（よしよし） [![Image from Gyazo](https://i.gyazo.com/96e93553bd419eac616d8c9d25042e05.png)](https://gyazo.com/96e93553bd419eac616d8c9d25042e05)
- ジェネレータの作成
```
# config/application.rb
class Application < Rails::Application
    config.generators do |g|
      g.test_framework :rspec,
      view_specs: false,
      helper_specs: false,
      routing_specs: false
    end
  end
```
>参考 
>RailsアプリにRSpecを導入する手順
>https://qiita.com/kmr0831/items/d932bb1d8fec0c668c49

## モデルスペック
まずはUserモデルに「メールアドレスがなければ無効な状態であること」をテストすることにする。バリデーションがしっかりしていれば通るはず。
```
RSpec.describe User, type: :model do
  #メールアドレスがなければ無効な状態であること    
  it "is invalid without an email address" do
     user = User.new(email: nil)
     user.valid?
     expect(user.errors[:email]).to include("が入力されていません。")
  end 
end
```
[![Image from Gyazo](https://i.gyazo.com/b66a6fc1627f833bf3e68248f9788ab4.png)](https://gyazo.com/b66a6fc1627f833bf3e68248f9788ab4)
- 1回目は「メールアドレス」という言葉が余計だとのことで、削除して2回目で通りました。（やった。）
- この後はファクトリーボットを導入してコードをスマートにする。
