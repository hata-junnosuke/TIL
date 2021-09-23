# オープニング & Dockerって何？
## Dockerって何？
- アプリを簡単に開発・デプロイできる仕組み
- Dockerがないと環境構築が大変
- メリット  
　1. 開発環境を簡単に用意できる 
　2. メンバー間の環境を統一 
　3. テスト環境や本番環境の統一 
# Linuxの基本コマンド
## Linuxって？
- OSの一種。OSはコンピュータを動かすための基本ソフトのこと。
- Dockerを使う時もOSはLinuxを使っているからLinuxを操作できないと開発できない。
- シェル・・Linuxのコマンドを受け取って結果を出力するためのソフトウェア
## ターミナルコマンド
[![Image from Gyazo](https://i.gyazo.com/d8b1d96ce98cf26a45f6c2adda314edb.png)](https://gyazo.com/d8b1d96ce98cf26a45f6c2adda314edb)
- ls
- pwd
- cd
- mkdir
- touch
- echo・・書き込み
- cat・・一覧
- less・・一覧（全画面）
- mv・・名前の付け直し
- rm・・削除
# Dockerを使ってみよう
- `$ docker run hello-world`・・起動
[![Image from Gyazo](https://i.gyazo.com/9e4ad4d9d5c3c47a1c5e995b0155c0e5.png)](https://gyazo.com/9e4ad4d9d5c3c47a1c5e995b0155c0e5)
[![Image from Gyazo](https://i.gyazo.com/cd246bd76a0d887c3b0234b6e27b4fb0.png)](https://gyazo.com/cd246bd76a0d887c3b0234b6e27b4fb0)
1. ターミナルでコマンドを実行する
2. Dockerデーモンにクライアントが伝える
3. イメージをレジストリから受け取る
4. コンテナを作成
5. コンテナが起動＝＞Hello 

# アプリを作成・実行しよう
[![Image from Gyazo](https://i.gyazo.com/1d41d900ab94b983182a2a652151b1bb.png)](https://gyazo.com/1d41d900ab94b983182a2a652151b1bb)
```
# main.rb
require 'webrick'

server = WEBrick::HTTPServer.new(
  DocumentRoot: './',
  BindAddress: '0.0.0.0',
  Port: 8000
)

server.mount_proc('/') do |req, res|
  res.body = 'hello'
end

server.start
```

```
＃Dockerfile・・イメージのひながた
FROM ruby:2.7

RUN mkdir /var/www
COPY main.rb /var/www

CMD ["ruby", "/var/www/main.rb"]
```

## イメージを作る
- `$ docker image build ~~~~`・・イメージを作成
- `$ docker image ls` ・・イメージの確認
## コンテナの作成
- `$ docker container run -p8000:8000 --name webrick sample~~`・・コンテナの作成と起動、ポートの接続、名前つけ、sample〜はイメージ名
- 停止・・コマンド＋C、`$docker container stop`
- 削除・・`$docker container rm コンテナ名`
- ログ・・　`$docker container logs コンテナ名`
- コマンド実行`$docker container exec コンテナ名 やりたいコマンド`
- 後片付け　`$docker system prune -a`使っていないものが一気に消える。
[![Image from Gyazo](https://i.gyazo.com/82e624954151f34fed196e326b46278c.png)](https://gyazo.com/82e624954151f34fed196e326b46278c)


# Dockerfileを作ろう
# Docker ComposeでRailsを構築しよう
# 本番環境に公開しよう
# CI/CDを構築しよう
