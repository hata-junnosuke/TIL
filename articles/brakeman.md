# はじめに

Railsアプリケーションを開発していて、「このコード、セキュリティ的に大丈夫かな?」と不安になったことはありませんか?
SQLインジェクションやXSS、CSRF対策の漏れなど、セキュリティの問題は気づかないうちに潜んでいることがあります。

Brakemanは、Railsアプリケーションのセキュリティ脆弱性を静的解析で自動検出してくれるgemです。コードを実行せずにスキャンするため、開発中でも気軽にチェックでき、本番環境に脆弱性を持ち込むリスクを大幅に削減できます。

本記事では、Brakemanの基本的な使い方を解説します。

# Brakemanとは

Brakemanは、Ruby on Rails専用の静的解析セキュリティスキャナーです。

## 何ができる

Brakemanの主要機能を紹介します。

### 1. セキュリティ脆弱性の自動検出

Brakemanは、以下のような幅広いセキュリティリスクを検出してくれます:

- **SQLインジェクション**: 不適切なSQL文の組み立て
- **XSS (クロスサイトスクリプティング)**: エスケープ漏れによる脆弱性
- **CSRF (クロスサイトリクエストフォージェリ)**: トークン設定の不備
- **マスアサインメント**: Strong Parametersの設定漏れ
- **機密情報の漏洩**: ハードコードされたパスワードやAPIキー
- **安全でないリダイレクト**: ユーザー入力を使った不適切なリダイレクト
- **スコープされていないクエリ**: Unscoped Findなど認可の問題

### 2. 実行結果のレポート

```bash
$ bundle exec brakeman

== Brakeman Report ==

Application Path: /path/to/your/app
Rails Version: 7.0.4
Brakeman Version: 6.0.0
Scan Date: 2025-10-05 10:00:00 +0900
Duration: 1.2 seconds

== Warnings ==

+------------+-------+--------+----------------------------------------+
| Confidence | Class | Method | Message                                |
+------------+-------+--------+----------------------------------------+
| High       | User  | search | Possible SQL injection                 |
| Medium     | Posts | show   | Unescaped parameter value              |
+------------+-------+--------+----------------------------------------+

2 warnings found
```

このように、脆弱性の内容、場所、信頼度レベルが一目で分かります。

### 3. 様々な出力形式に対応

- ターミナル出力（デフォルト）
- HTML形式のレポート
- JSON形式（CI/CDツールとの連携に便利）
- CSV形式
- Markdown形式

# 導入

## インストール

Gemfileに以下を追加:

```ruby
group :development do
  gem 'brakeman', require: false
end
```

bundleコマンドを実行:

```bash
bundle install
```

**ポイント**: `require: false` を指定することで、Railsアプリケーションの起動時に読み込まれないようにします。Brakemanはコマンドラインツールとして使うため、自動読み込みは不要です。

# 使用方法

## 基本的な使い方

### 1. 手動でスキャン実行

Railsプロジェクトのルートディレクトリで実行:

```bash
bundle exec brakeman
```

これだけで、プロジェクト全体のセキュリティスキャンが開始されます。

### 2. すべての警告レベルを表示

デフォルトでは信頼度が高い警告のみ表示されますが、低信頼度の警告も含めて確認したい場合:

```bash
bundle exec brakeman -w1
```

`-w1` で、信頼度レベル1（Low）以上の警告をすべて表示します。
- `-w1`: 低信頼度以上（すべて）
- `-w2`: 中信頼度以上（デフォルト）
- `-w3`: 高信頼度のみ

### 3. HTMLレポートを出力

ブラウザで見やすい形式でレポートを出力:

```bash
bundle exec brakeman -o report.html
```

生成された `report.html` をブラウザで開くと、脆弱性の詳細が見やすく表示されます。

# よく使うカスタマイズ

## 設定ファイルの活用

`config/brakeman.yml` を作成して、よく使うオプションを設定しておくと便利です:

```yaml
---
# すべてのチェック項目を実行
:run_all_checks: true

# 警告の最低信頼度レベル（1: Low, 2: Medium, 3: High）
:min_confidence: 2

# 警告が見つかった場合、終了コードを0以外にする（CI/CD用）
:exit_on_warn: true

# 出力フォーマット
:output_format: :text

# レポートの出力先
# :output_files:
#   - report.html

# 無視するファイルやディレクトリ
:skip_files:
  - vendor/
  - node_modules/

# HTMLレポートで警告をまとめる
:collapse_mass_assignment: true
```

この設定ファイルがあれば、`bundle exec brakeman` だけでカスタム設定が適用されます。

## 安全なメソッドの登録

特定のメソッドが安全であることが分かっている場合、誤検知を防ぐために登録できます:

```yaml
---
:safe_methods:
  - sanitize_input
  - custom_escape_method
```

## 警告の無視設定

### 対話的に無視リストを作成

既に出ている警告の中で、問題ないと判断したものを無視リストに追加:

```bash
bundle exec brakeman -I
```

このコマンドを実行すると、対話モードが起動します:

```
Input file: |config/brakeman.ignore|
# Enterキーを押して続行

1. Inspect all warnings
2. Hide previously ignored warnings
3. Skip - use current ignore configuration
? 1
# すべての警告を確認する場合は「1」を入力

------------------------------
Confidence: High
Category: SQL Injection
Message: Possible SQL injection
Code: User.where("name = '#{params[:name]}'")
File: app/controllers/users_controller.rb
Line: 15
------------------------------
Actions:
i - Add warning to ignore list
n - Add warning to ignore list and add note
s - Skip this warning
q - Quit
? i
# 無視する場合は「i」を入力
```

この操作で `config/brakeman.ignore` ファイルが生成され、指定した警告は以降表示されなくなります。

# 実践的な使い方

## 開発フローへの組み込み

### 1. コミット前のチェック

Git hooksを使って、コミット前に自動チェック:

`.git/hooks/pre-commit` を作成（または既存のファイルに追記）:

```bash
#!/bin/bash

echo "Running Brakeman security scan..."
bundle exec brakeman -z --no-pager --quiet

if [ $? -ne 0 ]; then
  echo "❌ Brakeman found security issues. Please fix them before committing."
  exit 1
fi

echo "✅ No security issues found!"
```

実行権限を付与:

```bash
chmod +x .git/hooks/pre-commit
```

### 2. 定期的なスキャン

週次や月次で、`-A -w1` オプションを使って徹底的なスキャンを実施します。（チームで分担して実行できればなお良し！）

```bash
bundle exec brakeman -A -w1 -o weekly_report.html
```

### 3. 新規プロジェクトへの早期導入

プロジェクト立ち上げ時にBrakemanを導入しておくことで、最初から脆弱性のないコードを書く習慣がつきます。

## 警告への対処方法

### High信頼度の警告

**必ず修正すべき**ものがほとんどです。

例: SQLインジェクション
```ruby
# ❌ 危険
User.where("name = '#{params[:name]}'")

# ✅ 安全
User.where(name: params[:name])
# または
User.where("name = ?", params[:name])
```

### Medium信頼度の警告

**コンテキストを確認して判断**が必要です。

例: Unscoped Find
```ruby
# ❌ 危険（他のユーザーのデータにアクセス可能）
Post.find(params[:id])

# ✅ 安全（現在のユーザーのデータのみ）
current_user.posts.find(params[:id])
```

### Low信頼度の警告

**誤検知の可能性**が高いですが、念のため確認します。

例えば、既に適切にサニタイズされているデータに対する警告など。

# まとめ

Brakemanは開発効率とセキュリティを両立させる強力なツールです。

- Railsアプリケーションの脆弱性を自動検出してくれる
- 静的解析なのでアプリを実行せずにチェック可能
- 設定ファイルや無視リストで、プロジェクトに合わせたカスタマイズが可能

## 注意点

- **完璧ではない**: Brakemanはあくまで静的解析ツールです。動的な脆弱性（実行時のみ発生する問題）は検出できません
- **誤検知がある**: すべての警告が本当の脆弱性とは限りません。コードを理解した上で判断が必要です
- **本番環境では不要**: `development` グループに入れることを忘れずに
- **定期的な更新**: Brakemanを定期的にアップデートして、新しい脆弱性パターンに対応しましょう
- **他のツールと併用**: RuboCop、RSpec、OWASP ZAPなどと組み合わせて、多層的なセキュリティ対策を実施しましょう

## 次のステップ

Brakemanを導入したら:

1. まず `-A` オプションで全項目をスキャン
2. High信頼度の警告から優先的に修正
3. 設定ファイルを作成してチームで共有

Brakemanを活用して、セキュアなRailsアプリケーション開発を習慣づけましょう！

# 参考リンク

- [Brakeman公式サイト](https://brakemanscanner.org/)
- [Brakeman GitHub](https://github.com/presidentbeef/brakeman)
- [Brakemanの警告タイプ一覧](https://brakemanscanner.org/docs/warning_types/)
