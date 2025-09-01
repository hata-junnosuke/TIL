# SimpleCovでテストカバレッジを可視化する

## はじめに

テストを書いているけれど、本当にすべてのコードがテストされているか不安になったことはありませんか？

SimpleCovは、Rubyアプリケーションのテストカバレッジを美しいHTMLレポートで可視化してくれるgemです。どの行がテストされていて、どの行がテストされていないかが一目で分かります。

本記事では、RSpecを使用したRailsアプリケーションでのSimpleCovの基本的な使い方を解説します。（Minitestでの使用方法は割愛させていただきます）

## 何ができる

### SimpleCovの主要機能

SimpleCovを導入すると、以下のことができるようになります。

### 1. カバレッジ率の可視化

テスト実行後に、全体のカバレッジ率とファイルごとの詳細が確認できます。

![スクリーンショット：SimpleCovのHTMLレポート](https://raw.githubusercontent.com/simplecov-ruby/simplecov/main/doc/sample_screenshot.png)

### 2. 未テストコードの特定

テストされていない行が赤色でハイライトされ、どこをテストすべきか一目瞭然です。

```ruby
class UserService
  def create_user(params)
    user = User.new(params)
    
    if user.save
      send_welcome_email(user)  # ✅ 緑：テスト済み
      true
    else
      log_error(user.errors)     # ❌ 赤：未テスト
      false
    end
  end
end
```

### 3. グループごとの分析

Controllers、Models、Servicesなど、グループごとにカバレッジを確認できます。

```
All Files ( 87.43% covered at 3.24 hits/line )

Group        Coverage
Controllers  95.2%
Models       82.3%
Services     76.4%
Mailers      91.7%
```

## 導入

### インストール

Gemfileに以下を追加：

```ruby
group :test do
  gem 'simplecov', require: false
end
```

bundleコマンドを実行：

```bash
bundle install
```

### 基本設定

`spec/spec_helper.rb`の**最上部**に以下を追加します。

⚠️ **重要**: 必ず他のコードより前に記述してください。そうしないと正確なカバレッジが測定できません。

```ruby
# spec/spec_helper.rb
require 'simplecov'
SimpleCov.start 'rails'

# この下に既存のコードが続く
require 'rails_helper'
# ...
```

これだけで基本的な設定は完了です！

## 使用方法

### テストの実行とレポート確認

#### 1. テストを実行

いつも通りRSpecを実行します：

```bash
# すべてのテストを実行
bundle exec rspec

# 特定のファイルのみ実行
bundle exec rspec spec/models/user_spec.rb
```

実行すると、ターミナルに簡易的な結果が表示されます：

```
Coverage report generated for RSpec to /myapp/coverage. 1089 / 1245 LOC (87.47%) covered.
```

#### 2. HTMLレポートを確認

`coverage/index.html`というファイルが自動生成されるので、ブラウザで開きます：

```bash
# Macの場合
open coverage/index.html

# Linuxの場合
xdg-open coverage/index.html

# WSLの場合
explorer.exe coverage/index.html
```

#### 3. レポートの見方

レポートでは以下の情報が確認できます：

- **全体のカバレッジ率**: ページ上部に大きく表示
- **ファイル一覧**: カバレッジ率でソート可能
- **詳細ビュー**: ファイル名をクリックすると、行ごとの実行状況が確認できる
  - 緑の行：テストで実行された
  - 赤の行：テストで実行されていない
  - 番号付き：その行が何回実行されたか

### よく使うカスタマイズ

#### 除外設定

テスト対象から除外したいファイルやディレクトリを指定できます：

```ruby
# spec/spec_helper.rb
require 'simplecov'

SimpleCov.start 'rails' do
  # 特定のディレクトリを除外
  add_filter '/spec/'      # specディレクトリ
  add_filter '/config/'    # 設定ファイル
  add_filter '/vendor/'    # 外部ライブラリ
  add_filter '/db/'        # マイグレーションファイル
end
```

#### グループ設定

ファイルをグループ分けして見やすくできます：

```ruby
# spec/spec_helper.rb
require 'simplecov'

SimpleCov.start 'rails' do
  # カスタムグループを追加
  add_group 'Services', 'app/services'
  add_group 'Serializers', 'app/serializers'
  add_group 'Workers', 'app/workers'
  add_group 'Api', 'app/controllers/api'
end
```

#### 最低カバレッジ率の設定

カバレッジが一定値を下回るとテストを失敗させることができます：

```ruby
# spec/spec_helper.rb
require 'simplecov'

SimpleCov.start 'rails' do
  minimum_coverage 80  # 80%未満でテスト失敗
  
  # ファイルごとの最低値も設定可能
  minimum_coverage_by_file 60
end
```

### .gitignoreへの追加

カバレッジレポートはGitで管理する必要がないので、`.gitignore`に追加しておきましょう：

```bash
# .gitignore
/coverage/
```

## まとめ

**SimpleCovは導入が簡単で効果が大きいツールです！**

- たった3行の設定で、テストカバレッジが可視化できる
- 未テストのコードが一目で分かるので、テストの抜け漏れを防げる
- チーム全体でカバレッジを意識した開発ができる

**注意点**

- カバレッジ100%を目指すのが目的ではありません。重要なビジネスロジックに集中しましょう
- SimpleCovを有効にするとテスト実行が少し遅くなります
- `spec_helper.rb`の最上部に記述することを忘れずに！

SimpleCovを導入して、テストの品質を可視化し、より安心してデプロイできる環境を作りましょう！

## 参考リンク

- [SimpleCov 公式リポジトリ](https://github.com/simplecov-ruby/simplecov)
- [SimpleCov Rails設定ガイド](https://github.com/simplecov-ruby/simplecov#getting-started)
- [RSpec公式ドキュメント](https://rspec.info/)
