

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
openapi: 3.0.1
info:
  title: API V1
  version: v1
paths:
  /api/users:
    get:
      summary: ユーザー一覧を取得
      responses:
        '200':
          description: 成功
          content:
            application/json:
              schema:
                type: array
                items:
                  type: object
                  properties:
                    id:
                      type: integer
                    name:
                      type: string
```

### 3. 複数バージョンのAPIドキュメント管理

v1、v2など、複数バージョンのAPIドキュメントを同時に管理・表示できます。

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

### よく使うカスタマイズ

#### 認証の設定

Bearer認証を使用する場合：

```yaml
# swagger/v1/swagger.yaml
components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

security:
  - bearerAuth: []

paths:
  /api/users/{id}:
    get:
      summary: ユーザー詳細を取得
      security:
        - bearerAuth: []
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
      responses:
        '200':
          description: 成功
```

#### レスポンス例の追加

```yaml
paths:
  /api/users:
    get:
      summary: ユーザー一覧を取得
      responses:
        '200':
          description: 成功
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
              examples:
                success:
                  value:
                    - id: 1
                      name: テスト太郎
                      email: test1@example.com
                    - id: 2
                      name: テスト花子
                      email: test2@example.com
```

#### UIのカスタマイズ

```ruby
# config/initializers/rswag_ui.rb
Rswag::Ui.configure do |c|
  c.swagger_endpoint '/api-docs/v1/swagger.yaml', 'API V1 Docs'
  
  # UIのカスタマイズオプション
  c.config_object = {
    defaultModelsExpandDepth: 1,  # モデルの展開レベル
    defaultModelExpandDepth: 1,   # 個別モデルの展開レベル
    docExpansion: 'list',         # 'none', 'list', 'full'
    filter: true,                 # 検索フィルターを表示
    showRequestDuration: true     # リクエスト時間を表示
  }
end
```

### その他の活用方法

#### RSpecと組み合わせた自動生成

Rswagには`rswag-specs`というgemもあり、RSpecのテストコードからOpenAPIドキュメントを自動生成することもできます。ただし、独特な記述方法が必要になるため、手動でYAMLを管理する方が柔軟性が高い場合もあります。

#### ドキュメントの分割管理

大規模なAPIの場合、ファイルを分割して管理することも可能：

```yaml
# swagger/v1/swagger.yaml
openapi: 3.0.1
info:
  title: My API V1
  version: v1

paths:
  /api/users:
    $ref: './paths/users.yaml#/users'
  /api/posts:
    $ref: './paths/posts.yaml#/posts'
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

Rswagを導入して、使いやすいAPIドキュメントを提供しましょう！

## 参考リンク

- [Rswag 公式リポジトリ](https://github.com/rswag/rswag)
- [OpenAPI Specification](https://swagger.io/specification/)
- [Swagger Editor（YAMLの作成に便利）](https://editor.swagger.io/)
