## はじめに

個人開発でRailsアプリケーションを運用していると、nginxの設定で苦労します。「とりあえず動けばいいや」でコピペしていた設定ファイルも、トラブルが起きたときに「これ何してるんだっけ？」となりがちです。

そこで今回は、実際に個人開発で使っているnginx.confを1行ずつ解説してみました。

## そもそもnginxって何？

nginxは「エンジンエックス」と読むWebサーバーソフトウェアです。主に以下の役割を担います。

### 1. Webサーバーとしての役割
静的ファイル（HTML、CSS、JavaScript、画像など）を高速に配信します。ApacheやIISと同じカテゴリのソフトウェアですが、nginxは特に高速・軽量で知られています。

### 2. リバースプロキシとしての役割
クライアント（ブラウザ）からのリクエストを受け取り、背後にあるアプリケーションサーバー（Rails/Puma、Node.js、Pythonなど）に転送します。これが今回の設定のメインの使い方です。

```
[ブラウザ] → [nginx] → [Railsアプリ(Puma)]
              ↑ここでリクエストを中継
```

### なぜnginxが必要なの？

「Railsアプリだけじゃダメなの？」と思うかもしれません。
実はRailsで使われているPumaは短時間に多くのリクエストが集中するような本番運用には適していません。
なので、本番運用ではnginxを前に置く構成が多く使われています。またnginxを置くことで以下のメリットを得ることができます。

- **静的ファイルの配信が高速**：画像やCSSはnginxが直接返すので、Railsの負荷が減る
- **複数リクエストの同時処理**：大量のアクセスを効率的に捌ける
- **セキュリティ**：直接アプリケーションサーバーを公開しなくて済む
- **柔軟な設定**：リダイレクト、アクセス制限、ロードバランシングなどが簡単

## 全体の設定ファイル

まずは設定ファイル全体を見てみましょう。コメント付きで要点を記載しています。

```nginx
user  nginx;
worker_processes  auto;  # CPUコア数に自動調整

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections 1024;  # 同時接続数
}

# HTTP設定
http {
  # バックエンドサーバー（Railsアプリ）
  upstream myapp {
    server unix:///myapp/tmp/sockets/puma.sock;  # Unixソケット通信
  }

  server {
    listen 80;
    server_name localhost;

    access_log /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;

    root /myapp/public;  # 静的ファイル配置先

    # タイムアウト設定（60秒）
    proxy_connect_timeout 60;
    proxy_read_timeout    60;
    proxy_send_timeout    60;

    client_max_body_size 100m;  # 最大アップロードサイズ
    error_page 404             /404.html;
    error_page 500 502 503 504 /500.html;
    keepalive_timeout 60;

    location / {
      try_files $uri @myapp;  # 静的ファイル優先、なければRailsへ
    }

    # リバースプロキシ設定
    location @myapp {
      proxy_set_header X-Real-IP $remote_addr;  # クライアントIP
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  # プロキシチェーン
      proxy_set_header Host $http_host;  # オリジナルHost
      proxy_pass http://myapp;  # Railsへ転送
    }
  }
}
```

## 設定ファイルの階層構造を理解しよう

nginx.confは階層構造になっており、これを理解することが設定を読み解く鍵です。

```
メインコンテキスト
├── events { }     # イベント処理の設定
└── http { }       # HTTPに関する設定
    ├── upstream { }  # バックエンドサーバーの定義
    └── server { }    # 仮想サーバーの設定
        ├── location / { }        # パスごとの設定
        ├── location /healthcheck { }
        └── location @myapp { }
```

**各階層の役割：**
- **メインコンテキスト（一番外側）**：nginx全体の基本設定
- **eventsブロック**：接続処理に関する設定
- **httpブロック**：HTTPサーバーとしての設定
- **serverブロック**：各仮想サーバーの設定（複数のドメインを扱う場合は複数記述可能）
- **locationブロック**：URLパスごとの細かい設定

この構造を頭に入れて、上から順番に解説していきます。

## 基本設定（メインコンテキスト）

### ユーザーとワーカープロセス

```nginx
user  nginx;
worker_processes  auto;
```

**`user nginx;`**
- nginxのワーカープロセスを実行するユーザーを指定
- セキュリティ上、root権限で動かさないようにnginxユーザーを使用

**`worker_processes auto;`**
- ワーカープロセスの数を指定
- `auto`にするとCPUコア数に合わせて自動設定してくれる
- 個人開発なら`auto`で十分（手動で`1`とか`2`とか指定してもOK）

### ログとPIDファイル

```nginx
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
```

**`error_log`**
- エラーログの出力先とログレベルを指定
- `warn`レベル以上のログを記録（debug < info < notice < warn < error < crit）

**`pid`**
- nginxのマスタープロセスのプロセスIDを保存するファイル
- サービスの起動・停止時に使用される

## イベント処理の設定

```nginx
events {
    worker_connections 1024;
}
```

**`worker_connections 1024;`**
- 1つのワーカープロセスが同時に処理できる最大接続数
- 個人開発なら1024で十分（アクセス数が増えたら調整）

## HTTPサーバーの設定

### アップストリーム（バックエンド）の定義

```nginx
upstream myapp {
    server unix:///myapp/tmp/sockets/puma.sock;
}
```

**`upstream`ブロック**
upstreamを使うメリット：
- 設定の一元管理：複数のlocationから同じバックエンドを参照する場合、一箇所で管理できる
- ロードバランシングの準備：将来的に複数のアプリケーションサーバーに分散したくなったとき、簡単に追加できる

```
# TCPポートの場合
server 127.0.0.1:3000;

# Unixソケットの場合
server unix:///myapp/tmp/sockets/puma.sock;
```
上記のようにTCPポートで設定することもできるが、Unixを使うと以下のメリットがある。
Unixソケットのメリット：
- 高速：ネットワークスタックを経由しないため、オーバーヘッドが少ない
- セキュア：外部からアクセスできない（ファイルシステム上の通信）
- ポート番号の管理不要：複数アプリを動かすときにポート番号の重複を気にしなくて良い

### サーバー設定の基本

```nginx
server {
    listen 80;
    server_name localhost;
    
    access_log /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;
    
    root /myapp/public;
```

**`listen 80;`**
- HTTPの標準ポート80番で待ち受け

**`server_name localhost;`**
- サーバー名を指定
- 本番環境では実際のドメイン名（例：`example.com`）を設定

**`root /myapp/public;`**
- ドキュメントルートの指定
- Railsのpublicディレクトリを指定（静的ファイルはここから配信）

### タイムアウトとサイズ制限

```nginx
proxy_connect_timeout 60;
proxy_read_timeout    60;
proxy_send_timeout    60;

client_max_body_size 100m;
keepalive_timeout 60;
```

**タイムアウト設定（全て60秒=1分）**
- `proxy_connect_timeout`: バックエンドへの接続確立のタイムアウト
- `proxy_read_timeout`: バックエンドからの応答待ちのタイムアウト
- `proxy_send_timeout`: バックエンドへのリクエスト送信のタイムアウト
- 通常の処理なら60秒で十分。重い処理がある場合は調整が必要

**`client_max_body_size 100m;`**
- クライアントからのリクエストボディの最大サイズ
- 画像や動画のアップロードがある場合は大きめに設定

**`keepalive_timeout 60;`**
- Keep-Alive接続のタイムアウト時間
- 接続を維持することで、複数のリクエストを効率的に処理

### エラーページの設定

```nginx
error_page 404             /404.html;
error_page 500 502 503 504 /500.html;
```

- 各HTTPステータスコードに対応するエラーページを指定
- Railsのpublic配下の静的HTMLファイルを表示

## ロケーション（パス）ごとの設定

### メインのルーティング

```nginx
location / {
    try_files $uri @myapp;
}
```

**`try_files`の動作**
1. まず`$uri`（リクエストされたパス）を静的ファイルとして探す
2. 見つからなければ`@myapp`（名前付きロケーション）にフォールバック
3. つまり、静的ファイルは直接nginxが返し、それ以外はRailsアプリへ

これにより、画像やCSS、JavaScriptなどの静的ファイルはnginxが直接配信し、動的なコンテンツのみRailsが処理するため効率的です。

### リバースプロキシの設定

```nginx
location @myapp {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_pass http://myapp;
}
```

**各ヘッダーの役割**

**`X-Real-IP $remote_addr;`**
- クライアントの実IPアドレスを転送
- Railsアプリ側でアクセス元のIPアドレスを取得できる

**`X-Forwarded-For $proxy_add_x_forwarded_for;`**
- プロキシチェーンの情報を転送
- 複数のプロキシを経由している場合の経路情報

**`Host $http_host;`**
- オリジナルのHostヘッダーを転送
- Railsアプリ側で正しいホスト名を認識できる

**`proxy_pass http://myapp;`**
- 最初に定義した`upstream myapp`にリクエストを転送

## この設定のポイント

### 1. Unixソケット通信の採用
同一サーバー内でnginxとPumaが動作しているので、TCPポートではなくUnixソケットを使用。これによりオーバーヘッドが減少し、パフォーマンスが向上します。

### 2. 静的ファイルの効率的な配信
`try_files`ディレクティブにより、静的ファイルはnginxが直接配信。Railsアプリケーションの負荷を軽減します。

### 3. 適切なタイムアウト設定
通常の処理を想定して60秒に設定。長すぎず短すぎない設定で、リソースを効率的に利用できます。

## nginxでこんなこともできる

基本設定以外にも、nginxではこんなことができます。必要に応じて追加してみてください。

### HTTPS対応

```nginx
server {
    listen 443 ssl;
    server_name example.com;
    
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
}
```

### リダイレクト設定

```nginx
# wwwありに統一
server {
    server_name example.com;
    return 301 https://www.example.com$request_uri;
}
```

### IP制限

```nginx
# 管理画面への接続を特定IPのみ許可
location /admin {
    allow 192.168.1.0/24;
    deny all;
}
```

### Basic認証

```nginx
location /private {
    auth_basic "Restricted";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

### レート制限（DDoS対策）

```nginx
# IPごとに毎秒10リクエストまで
limit_req_zone $binary_remote_addr zone=limit:10m rate=10r/s;

location /api/ {
    limit_req zone=limit burst=5;
}
```

### ロードバランシング

```nginx
# 複数サーバーへの負荷分散
upstream backend {
    server app1:3000;
    server app2:3000;
    server app3:3000;
}
```

### 静的ファイルのキャッシュ設定

```nginx
# 画像やCSS、JSをブラウザにキャッシュ
location ~* \.(jpg|css|js)$ {
    expires 1y;
    add_header Cache-Control "public";
}
```

### WebSocket対応

```nginx
location /cable {
    proxy_pass http://myapp;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
}
```

### 設定変更後は必ず検証

```bash
# 構文チェック
sudo nginx -t

# リロード
sudo nginx -s reload
```

## まとめ

個人開発では「とりあえず動く」設定から始めて、必要に応じてチューニングしていくのがおすすめです。
最近ではロードバランサーなどを使うことで nginx を使わなくても良いのではという議論もあるようなので、そちらも追ってみて、アプリに合わせた構成を組んでみてください。

この記事が、nginxの設定で悩んでいる方の参考になれば幸いです。設定をいじる際は必ずバックアップを取ってから試してくださいね！

## 参考リンク

- [nginx公式ドキュメント](http://nginx.org/en/docs/)
