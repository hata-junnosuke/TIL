

## はじめに

APIを開発していて、「ドキュメントの更新を忘れていた」「実装とドキュメントがズレている」という経験はありませんか？

Rswagは、Swagger（OpenAPI）形式のAPIドキュメントをRailsアプリケーションに統合できるgemです。Swagger UIを使って、インタラクティブなAPIドキュメントを提供できます。

本記事では、Rswagの基本的な使い方を解説します。

## 何ができる

### Rswagの主要機能

Rswagを導入すると、以下のことができるようになります。

### 1. Swagger UIでインタラクティブなAPIドキュメントを表示

ブラウザから直接APIを試すことができる、美しいドキュメントUIが使えます。

![スクリーン ショット 2025-09-16 に 19.03.52 午後.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2517030/f07398d3-80db-46a6-a349-ffdedf5e499b.png)

### 2. OpenAPI（Swagger）仕様のYAML/JSONファイルを配信

作成したOpenAPIドキュメントをエンドポイントとして公開できます。

```yaml
# swagger/v1/swagger.yaml
---
openapi: 3.0.1
info:
  title: Runmates API
  version: v1
  description: Runmates - ランニング管理アプリケーションのAPI仕様書
paths:
  "/api/v1/auth":
    post:
      summary: User registration
      tags:
      - Authentication
      description: 新規ユーザーアカウントを作成します。成功時には確認メールが送信されます。
      parameters: []
      responses:
        '200':
          description: 登録成功
          content:
            application/json:
              schema:
                type: object
                properties:
                  status:
                    type: string
                  data:
                    type: object
                    properties:
                      id:
                        type: integer
                      email:
                        type: string
                      name:
                        type:
                        - string
                        - 'null'
                      provider:
                        type: string
                      uid:
                        type: string
                      created_at:
                        type: string
                        format: datetime
                      updated_at:
                        type: string
                        format: datetime
        '422':
          description: バリデーションエラー
          content:
            application/json:
              schema:
                type: object
                properties:
                  status:
                    type: string
                  errors:
                    type: array
                    items:
                      type: string
```

## 導入

### インストール

Gemfileに以下を追加：

```ruby
gem 'rswag-api'
gem 'rswag-ui'
```

bundleコマンドを実行：

```bash
bundle install
```

### 初期設定

インストール後、以下のコマンドで設定ファイルを生成：

```bash
rails g rswag:api:install
rails g rswag:ui:install
```

以下のファイルが生成されます：
- `config/initializers/rswag_api.rb` - API設定
- `config/initializers/rswag_ui.rb` - UI設定

## 使用方法

### 基本的な使い方

#### 1. OpenAPIドキュメントの作成

`swagger`ディレクトリを作成し、OpenAPI形式のドキュメントを配置：

```bash
mkdir -p swagger/v1
```

```yaml
# swagger/v1/swagger.yaml
openapi: 3.0.1
info:
  title: My API V1
  version: v1
  description: APIの説明文
servers:
  - url: http://localhost:3000
    description: 開発環境
  - url: https://api.example.com
    description: 本番環境

paths:
  /api/users:
    get:
      summary: ユーザー一覧を取得
      tags:
        - Users
      responses:
        '200':
          description: 成功
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
    
    post:
      summary: ユーザーを作成
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - name
                - email
              properties:
                name:
                  type: string
                  example: テスト太郎
                email:
                  type: string
                  format: email
                  example: test@example.com
      responses:
        '201':
          description: 作成成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '422':
          description: バリデーションエラー

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: テスト太郎
        email:
          type: string
          format: email
          example: test@example.com
        created_at:
          type: string
          format: date-time
          example: '2024-01-01T00:00:00Z'
```

#### 2. Rswagの設定

```ruby
# config/initializers/rswag_ui.rb
Rswag::Ui.configure do |c|
  # swagger_endpointで表示するドキュメントのパスと名前を指定
  c.openapi_endpoint '/api-docs/v1/swagger.yaml', 'API V1 Docs'
  
  # 複数バージョンがある場合
  # c.openapi_endpoint '/api-docs/v2/swagger.yaml', 'API V2 Docs'
end
```

```ruby
# config/initializers/rswag_api.rb
Rswag::Api.configure do |c|
  # Swaggerファイルが格納されているルートフォルダを指定
  c.openapi_root = "#{Rails.root}/swagger"
end
```

#### 3. ルーティングの設定

```ruby
# config/routes.rb
Rails.application.routes.draw do
  mount Rswag::Ui::Engine => '/api-docs'
  mount Rswag::Api::Engine => '/api-docs'
  
  # APIのルーティング
  namespace :api do
    resources :users
  end
end
```

#### 4. Swagger UIで確認

Railsサーバーを起動：

```bash
rails s
```

ブラウザで `http://localhost:3000/api-docs` にアクセスすると、Swagger UIが表示されます。

### その他の活用方法

#### RSpecと組み合わせた自動生成

Rswagには`rswag-specs`というgemもあり、RSpecのテストコードからOpenAPIドキュメントを自動生成することもできます。ただし、独特な記述方法が必要になるため、手動でYAMLを管理する方が柔軟性が高い場合もあります。

#### Basic認証でSwagger UIを保護
開発環境でSwagger UIへのアクセスを制限したい場合、Basic認証を設定できます：
```ruby
# config/initializers/rswag_ui.rb
Rswag::Ui.configure do |c|
  # Swagger UIのエンドポイント設定
  c.openapi_endpoint '/api-docs/v1/swagger.yaml', 'API V1 Docs'
  
  # Basic認証を有効化
  c.basic_auth_enabled = true
  c.basic_auth_credentials 'username', 'password'
end
```
これで、http://localhost:3000/api-docs にアクセスすると、ユーザー名とパスワードの入力を求められるようになります。
注意点：
この設定はSwagger UIのエンドポイントのみに適用され、APIエンドポイントには影響しません。
本番環境では環境変数を使用するなど、認証情報の管理に注意してください。

```ruby
# 環境変数を使用した例
c.basic_auth_credentials ENV['SWAGGER_USERNAME'], ENV['SWAGGER_PASSWORD']
```
## まとめ

**RswagでインタラクティブなAPIドキュメントを提供しましょう！**

- Swagger UIで実際にAPIを試せるドキュメントが作れる
- OpenAPI仕様に準拠した標準的なドキュメント管理ができる
- フロントエンド開発者がAPIの動作を確認しやすくなる

**注意点**

- YAMLファイルの構文エラーに注意（インデントなど）
- 大規模なAPIの場合はファイル分割を検討すると良いでしょう
- セキュリティ情報（本番環境のURLなど）の管理に注意

**私の使い方**
`rswag-specs`で自動生成はしていないのですが、[Swagger Editor](https://editor.swagger.io/)を使うと構文エラーをリアルタイムでチェックしながら作成できて便利です。最近はAIを活用して、APIの仕様を伝えるだけでOpenAPI形式のYAMLを生成してもらうことも多いです。手作業での記述ミスが減り、開発効率が大幅に向上しています！

Rswagを導入して、使いやすいAPIドキュメントを提供しましょう！

## 参考リンク

- [Rswag 公式リポジトリ](https://github.com/rswag/rswag)
- [OpenAPI Specification](https://swagger.io/specification/)
- [Swagger Editor（YAMLの作成に便利）](https://editor.swagger.io/)
