## はじめに

個人開発でRailsアプリケーションを運用していると、必ずと言っていいほどnginxの設定でつまずきます。「とりあえず動けばいいや」でコピペしていた設定ファイルも、トラブルが起きたときに「これ何してるんだっけ？」となりがち。

そこで今回は、実際に個人開発で使っているnginx.confを1行ずつ解説してみました。同じような構成の方の参考になれば幸いです。

## そもそもnginx（エンジンエックス）って何？

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

「Railsアプリだけじゃダメなの？」と思うかもしれません。実は以下の理由でnginxを前に置くメリットがあります。

- **静的ファイルの配信が高速**：画像やCSSはnginxが直接返すので、Railsの負荷が減る
- **複数リクエストの同時処理**：大量のアクセスを効率的に捌ける
- **セキュリティ**：直接アプリケーションサーバーを公開しなくて済む
- **柔軟な設定**：リダイレクト、アクセス制限、ロードバランシングなどが簡単

## 全体の設定ファイル

まずは設定ファイル全体を見てみましょう。

```nginx
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
  upstream myapp {
    server unix:///myapp/tmp/sockets/puma.sock;
  }

  server {
    listen 80;
    server_name localhost;

    access_log /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;

    root /myapp/public;

    proxy_connect_timeout 600;
    proxy_read_timeout    600;
    proxy_send_timeout    600;

    client_max_body_size 100m;
    error_page 404             /404.html;
    error_page 505 502 503 504 /500.html;
    keepalive_timeout 600;
    
    location /healthcheck {
      root   /usr/share/nginx/html;
      empty_gif;
      break;
    }

    location / {
      try_files $uri @myapp;
    }

    location @myapp {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_pass http://myapp;
    }
  }
}
```

では、上から順番に解説していきます。

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
- バックエンドのアプリケーションサーバー（Puma）への接続方法を定義
- `myapp`は任意の名前（後で`proxy_pass`で参照する）

**`server unix:///...`**
- UnixソケットでPumaと通信
- TCPポート（例：`server 127.0.0.1:3000`）よりも高速
- 同一サーバー内の通信なのでソケット通信がおすすめ

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
proxy_connect_timeout 600;
proxy_read_timeout    600;
proxy_send_timeout    600;

client_max_body_size 100m;
keepalive_timeout 600;
```

**タイムアウト設定（全て600秒=10分）**
- `proxy_connect_timeout`: バックエンドへの接続確立のタイムアウト
- `proxy_read_timeout`: バックエンドからの応答待ちのタイムアウト
- `proxy_send_timeout`: バックエンドへのリクエスト送信のタイムアウト
- 重い処理や大きなファイルアップロードがある場合は長めに設定

**`client_max_body_size 100m;`**
- クライアントからのリクエストボディの最大サイズ
- 画像や動画のアップロードがある場合は大きめに設定

**`keepalive_timeout 600;`**
- Keep-Alive接続のタイムアウト時間
- 接続を維持することで、複数のリクエストを効率的に処理

### エラーページの設定

```nginx
error_page 404             /404.html;
error_page 505 502 503 504 /500.html;
```

- 各HTTPステータスコードに対応するエラーページを指定
- Railsのpublic配下の静的HTMLファイルを表示

## ロケーション（パス）ごとの設定

### ヘルスチェック用エンドポイント

```nginx
location /healthcheck {
    root   /usr/share/nginx/html;
    empty_gif;
    break;
}
```

**用途**
- ロードバランサーやモニタリングツールからの死活監視用
- 軽量な1x1の透明GIFを返すだけなので、アプリケーションに負荷をかけない

**`empty_gif;`**
- nginxの組み込み機能で、1x1ピクセルの透明GIFを返す
- アプリケーションサーバーを経由しないので高速

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

### 3. 柔軟なタイムアウト設定
個人開発では重い処理も発生しがちなので、タイムアウトを長めに設定。本番環境では要件に応じて調整が必要です。

### 4. ヘルスチェックの実装
`/healthcheck`エンドポイントを用意することで、サービスの死活監視が簡単に実装できます。

## トラブルシューティングTips

### 502 Bad Gatewayが出る場合
- Pumaが起動しているか確認
- ソケットファイルのパスが正しいか確認
- ソケットファイルの権限を確認

### 413 Request Entity Too Largeが出る場合
- `client_max_body_size`を増やす

### タイムアウトエラーが出る場合
- 各種タイムアウト設定を見直す
- アプリケーション側の処理を最適化

## まとめ

nginxの設定は一見複雑に見えますが、各ディレクティブの役割を理解すれば、そんなに難しくありません。個人開発では「とりあえず動く」設定から始めて、必要に応じてチューニングしていくのがおすすめです。

この記事が、nginxの設定で悩んでいる方の参考になれば幸いです。設定をいじる際は必ずバックアップを取ってから試してくださいね！

## 参考リンク

- [nginx公式ドキュメント](http://nginx.org/en/docs/)
- [Rails + Puma + Nginxの構成](https://github.com/puma/puma/blob/master/docs/nginx.md)
