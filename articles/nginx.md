## はじめに

個人開発でRailsアプリケーションを運用していると、nginxの設定で苦労しませんか？
「とりあえず動けばいいや」でコピペしていた設定ファイルも、トラブルが起きたときに「これ何してるんだっけ？」となりがちです。

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
なので、本番運用では多くのリクエストを捌くことができるnginxを前に置く構成が多く使われています。それ以外にもnginxを置くことで以下のメリットを得ることができます。

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

### upstreamでバックエンドを指定

```nginx
upstream myapp {
    server unix:///myapp/tmp/sockets/puma.sock;
}
```

**`upstream`ブロック**
upstreamを使うメリット：
- 設定の一元管理：複数のlocationから同じバックエンドを参照する場合、一箇所で管理できる
- ロードバランシングの準備：将来的に複数のアプリケーションサーバーに分散したくなったとき、簡単に追加できる（後述でちょこっと触れます）

下記のようにTCPポートで設定することもできます。
```
# TCPポートの場合
server 127.0.0.1:3000;

# Unixソケットの場合
server unix:///myapp/tmp/sockets/puma.sock;
```
ですが、TCPポートではなくUnixを使うと以下のメリットがある。
Unixソケットのメリット：
- 高速：ネットワークスタックを経由しないため、オーバーヘッドが少ない
- セキュア：外部からアクセスできない（ファイルシステム上の通信）
- ポート番号の管理不要：複数アプリを動かすときにポート番号の重複を気にしなくて良い

## サーバー設定の基本

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
- 今回は同じサーバー内に nginx と Rails があるのでlocalhost

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

## 【前提知識】nginxの変数を理解しよう

これからロケーション（パス）ごとの設定を見ていきますが、コードの中に`$uri`や`$remote_addr`といった`$`で始まる記述が登場します。これらはnginxの**変数**で、リクエストの情報を動的に参照するために使われます。

ここでは、今回の設定で実際に使っている主要な変数を先に理解しておきましょう。

### 今回の設定で使う変数

**`$uri`**
- リクエストされたURLのパス部分（クエリ文字列を除いた正規化された形）
- 例：`https://example.com/images/logo.png?v=1` にアクセスした場合、`$uri` = `/images/logo.png`

**`$remote_addr`**
- アクセスしてきたクライアント（ブラウザ）のIPアドレス
- 例：`192.168.1.100`

**`$http_host`**
- リクエストのHostヘッダーの値（アクセスされたドメイン名）
- 例：`example.com` や `www.example.com`

**`$proxy_add_x_forwarded_for`**
- プロキシを経由した場合の経路情報を保持する特殊な変数
- 既存のX-Forwarded-Forヘッダーの値に、現在のクライアントIPを追加したもの

これらの変数がどのように活用されているか、実際のコードで確認していきましょう。

## ロケーション（パス）ごとの設定

### メインのルーティング

```nginx
location / {
    try_files $uri @myapp;
}
```

**`try_files`の動作**

この1行には重要な要素が2つあります。

**1. `$uri`変数の役割**

`$uri`はnginxの組み込み変数で、リクエストされたパスを表します。

```
例：ユーザーが https://example.com/images/logo.png にアクセス
→ $uri = /images/logo.png
```

nginxはまず、この`$uri`の値をもとに物理ファイルが存在するかチェックします。具体的には`root`で指定したディレクトリ（`/myapp/public`）から該当ファイルを探します。

```
/myapp/public/images/logo.png が存在するか？
→ YES: ファイルを直接返す（nginxが処理）
→ NO: 次のステップへ
```

**2. `@myapp`名前付きロケーションとは**

`@`で始まる記述は「名前付きロケーション（named location）」と呼ばれる特殊な内部リダイレクト先です。

通常のlocationとの違い：
- 通常：`location /path { }` → URLパスに直接マッチ
- 名前付き：`location @name { }` → 他のlocationから内部的に呼び出される専用の設定

`@myapp`は外部から直接アクセスできず、`try_files`などから内部的にのみ呼び出されます。

**処理の流れをまとめると：**

```
1. リクエスト: /images/logo.png
   ↓
2. $uri (/images/logo.png) で静的ファイルを探す
   ↓
3-a. ファイルが見つかる
   → nginxが直接配信（高速！）
   
3-b. ファイルが見つからない
   → @myapp にフォールバック
   → location @myapp { } の設定を実行
   → Railsアプリに転送
```

この仕組みにより、画像やCSS、JavaScriptなどの静的ファイルはnginxが直接配信し、動的なコンテンツ（DBアクセスが必要なページなど）のみRailsが処理するため、非常に効率的です。

**なぜ`@myapp`という名前なのか？**

これは任意の名前を付けられます。`@app`、`@backend`、`@rails`など何でもOK。ただし後で`proxy_pass http://myapp`として`upstream myapp`を参照するので、わかりやすく同じ名前にしています。

### リバースプロキシの設定

```nginx
location @myapp {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_pass http://myapp;
}
```

ここが静的ファイルが見つからなかった場合に実行される処理です。

**各ヘッダーの役割**

**`X-Real-IP $remote_addr;`**
- クライアントの実IPアドレスを転送
- Railsアプリ側でアクセス元のIPアドレスを取得できる
- `$remote_addr`変数にはクライアントのIPが格納されている

**`X-Forwarded-For $proxy_add_x_forwarded_for;`**
- プロキシチェーンの情報を転送
- 複数のプロキシを経由している場合の経路情報
- `$proxy_add_x_forwarded_for`は既存のX-Forwarded-Forに現在のクライアントIPを追加

**`Host $http_host;`**
- オリジナルのHostヘッダーを転送
- Railsアプリ側で正しいホスト名を認識できる
- `$http_host`変数にはリクエストのHostヘッダー値が入る

**`proxy_pass http://myapp;`**
- 最初に定義した`upstream myapp`にリクエストを転送
- この`myapp`は`upstream myapp { ... }`で定義したバックエンドサーバーを指す

## この設定のポイント

### 1. Unixソケット通信の採用
同一サーバー内でnginxとPumaが動作しているので、TCPポートではなくUnixソケットを使用。これによりオーバーヘッドが減少し、パフォーマンスが向上します。

### 2. 静的ファイルの効率的な配信
`try_files`ディレクティブにより、静的ファイルはnginxが直接配信。Railsアプリケーションの負荷を軽減します。

### 3. 適切なタイムアウト設定
通常の処理を想定して60秒に設定。長すぎず短すぎない設定で、リソースを効率的に利用できます。

### 4. 名前付きロケーションによる柔軟な制御
`@myapp`を使うことで、静的ファイル処理とアプリケーション処理を明確に分離。メンテナンス性と可読性が向上します。

---

## 【番外編】nginxでこんなこともできる

ここまでが基本的な設定の解説でした。以下は、必要に応じて追加できる応用的な設定例です。すべてを使う必要はありませんが、「こういうこともできるんだ」と知っておくと、将来役立つかもしれません。

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

nginxの設定を理解する上で、特に重要なポイントは以下の3つです。

### 1. 階層構造を理解する

nginx.confは入れ子構造になっており、この構造を理解することが設定を読み解く最大の鍵です。

```
メインコンテキスト（nginx全体の設定）
└── http（HTTPサーバー全体の設定）
    ├── upstream（バックエンドサーバーの定義）
    └── server（仮想サーバーごとの設定）
        └── location（URLパスごとの詳細設定）
```

どの階層で何を設定するのかを意識すると、設定ファイル全体の見通しが良くなります。外側の階層（メインコンテキスト）では全体的な動作を、内側の階層（locationなど）では具体的な処理を記述するという構造になっています。

### 2. 変数と名前付きロケーションの活用

`$uri`変数と`@myapp`名前付きロケーションの仕組みを理解すると、nginxの動作がぐっと理解しやすくなります。

- `$uri`：リクエストされたパスを表す組み込み変数
- `@myapp`：内部的に呼び出される名前付きロケーション

この2つの組み合わせで、静的ファイルの高速配信とアプリケーションへの適切なルーティングが実現されています。

### 3. 段階的に理解を深める

個人開発では「とりあえず動く」設定から始めて、必要に応じてチューニングしていくのがおすすめです。まずは基本的な構成（メインコンテキスト→http→server→location）を理解し、その後で番外編のような応用設定に挑戦すると良いでしょう。

最近ではロードバランサーなどを使うことでnginxを使わなくても良いのではという議論もあるようなので、そちらも追ってみて、アプリに合わせた構成を組んでみてください。

この記事が、nginxの設定で悩んでいる方の参考になれば幸いです。
## 参考リンク

- [nginx公式ドキュメント](http://nginx.org/en/docs/)
