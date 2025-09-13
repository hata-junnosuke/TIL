## はじめに

Railsでモデルファイルを開いたとき、「このモデルにはどんなカラムがあったっけ？」と、わざわざ`schema.rb`を確認したり、DBクライアントを開いたりしていませんか？

Annotateは、モデルファイルの先頭にテーブルのスキーマ情報をコメントとして自動追記してくれるgemです。カラム名、型、制約などが一目で分かるようになり、開発効率が大幅に向上します。

本記事では、Annotateの基本的な使い方を解説します。

## 何ができる

### Annotateの主要機能

Annotateを導入すると、以下のような情報がモデルファイルに自動で追記されます。

### 1. モデルファイルへのスキーマ情報追記

```ruby
# == Schema Information
#
# Table name: users
#
#  id                     :bigint           not null, primary key
#  email                  :string(255)      default(""), not null
#  name                   :string(255)
#  age                    :integer
#  admin                  :boolean          default(FALSE), not null
#  encrypted_password     :string(255)      default(""), not null
#  remember_created_at    :datetime
#  created_at             :datetime         not null
#  updated_at             :datetime         not null
#
# Indexes
#
#  index_users_on_email  (email) UNIQUE
#

class User < ApplicationRecord
  # モデルのコード
end
```

### 2. 自動更新

マイグレーション実行時に自動でスキーマ情報を更新できます。

```bash
rails db:migrate
# => モデルファイルのスキーマ情報も自動更新される
```

### 3. 他のファイルへの追記も可能

モデル以外にも以下のファイルに情報を追記できます：

- Factoryファイル（spec/factories/）
- Fixtureファイル（test/fixtures/）
- ルーティング情報（config/routes.rb）

## 導入

### インストール

Gemfileに以下を追加：

```ruby
group :development do
  gem 'annotate'
end
```

bundleコマンドを実行：

```bash
bundle install
```

### 初期設定

インストール後、以下のコマンドで設定ファイルを生成：

```bash
bundle exec rails g annotate:install
```

`lib/tasks/auto_annotate_models.rake`が生成されます。

## 使用方法

### 基本的な使い方

#### 1. 手動でアノテーションを追加

既存のモデルすべてにスキーマ情報を追加：

```bash
bundle exec annotate --models
```

特定のモデルのみ更新：

```bash
bundle exec annotate --models --include User,Post
```

#### 2. マイグレーション時の自動実行

`lib/tasks/auto_annotate_models.rake`が存在すれば、マイグレーション実行時に自動でアノテーションが更新されます：

```bash
rails db:migrate
# => 自動でモデルファイルが更新される

rails db:rollback
# => ロールバック時も自動更新
```
※もしこの設定を無効化したいときは`lib/tasks/auto_annotate_models.rake`の以下の部分を変更してください
```ruby
# 初期ではfalseになっています
'skip_on_db_migrate'   => 'true',
```
#### 3. アノテーションの削除

すべてのアノテーションを削除したい場合：

```bash
bundle exec annotate --delete
```

### よく使うカスタマイズ

#### 表示位置の変更

デフォルトではファイルの先頭に追記されますが、末尾に変更することも可能：

```ruby
# lib/tasks/auto_annotate_models.rake
Annotate.set_defaults(
  'position_in_class' => 'after',  # beforeからafterに変更
  # その他の設定...
)
```

#### 追記対象ファイルの設定

```ruby
# lib/tasks/auto_annotate_models.rake
Annotate.set_defaults(
  'models' => 'true',           # モデル
  'routes' => 'false',          # ルーティング（config/routes.rb）
  'fixtures' => 'true',          # Fixture
  'factories' => 'true',         # FactoryBot
  'serializers' => 'false',      # Serializer
  'controllers' => 'false',      # Controller
  # その他の設定...
)
```

#### 表示フォーマットのカスタマイズ

```ruby
# lib/tasks/auto_annotate_models.rake
Annotate.set_defaults(
  'format_bare' => 'false',           # trueにするとシンプルな表示
  'format_markdown' => 'false',       # trueにするとMarkdown形式
  'show_indexes' => 'true',          # インデックス情報を表示
  'show_foreign_keys' => 'true',     # 外部キー情報を表示
  'show_complete_foreign_keys' => 'false',  # 外部キーの詳細情報
  # その他の設定...
)
```

### 特定のモデルを除外

アノテーションを追加したくないモデルがある場合：

```ruby
# lib/tasks/auto_annotate_models.rake
Annotate.set_defaults(
  'ignore_model_sub_dir' => 'false',
  'ignore_unknown_models' => 'true',
  'exclude_tests' => 'false',
  'exclude_fixtures' => 'false',
  'exclude_factories' => 'false',
  'exclude_serializers' => 'false',
  'exclude_scaffolds' => 'true',
  'exclude_controllers' => 'true',
  'exclude_helpers' => 'true',
  'ignore_model_names' => ['ApplicationRecord', 'AdminUser'],  # 除外するモデル名
  # その他の設定...
)
```

## まとめ

**Annotateは開発効率を大幅に向上させる便利なツールです！**

- モデルファイルを見るだけでDBスキーマが分かる
- マイグレーション時に自動更新されるので、常に最新の情報が保たれる
- チーム開発での認識齟齬を防げる

**注意点**

- 本番環境では不要なので、`development`グループに入れることを忘れずに
- Gitでコンフリクトしやすいので、チームで導入タイミングを相談すると良いでしょう
- カスタマイズ設定はチーム全体で統一することが重要です
- 特にモデルのカラムが多くなってくると見栄えが良くないかもしれないです

Annotateを導入して、より快適なRails開発環境を構築しましょう！

## 参考リンク

- [Annotate 公式リポジトリ](https://github.com/ctran/annotate_models)
