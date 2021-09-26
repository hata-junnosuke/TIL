# Docker超入門講座 合併版 | ゼロから実践する4時間のフルコース
## オープニング & Dockerって何？
### Dockerって何？
- アプリを簡単に開発・デプロイできる仕組み
- Dockerがないと環境構築が大変
- メリット  
　1. 開発環境を簡単に用意できる 
　2. メンバー間の環境を統一 
　3. テスト環境や本番環境の統一 
## Linuxの基本コマンド
### Linuxって？
- OSの一種。OSはコンピュータを動かすための基本ソフトのこと。
- Dockerを使う時もOSはLinuxを使っているからLinuxを操作できないと開発できない。
- シェル・・Linuxのコマンドを受け取って結果を出力するためのソフトウェア
### ターミナルコマンド
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
## Dockerを使ってみよう
- `$ docker run hello-world`・・起動
[![Image from Gyazo](https://i.gyazo.com/9e4ad4d9d5c3c47a1c5e995b0155c0e5.png)](https://gyazo.com/9e4ad4d9d5c3c47a1c5e995b0155c0e5)
[![Image from Gyazo](https://i.gyazo.com/cd246bd76a0d887c3b0234b6e27b4fb0.png)](https://gyazo.com/cd246bd76a0d887c3b0234b6e27b4fb0)
1. ターミナルでコマンドを実行する
2. Dockerデーモンにクライアントが伝える
3. イメージをレジストリから受け取る
4. コンテナを作成
5. コンテナが起動＝＞Hello 

## アプリを作成・実行しよう
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

### イメージを作る
- `$ docker image build ~~~~`・・イメージを作成
- `$ docker image ls` ・・イメージの確認
### コンテナの作成
- `$ docker container run -p8000:8000 --name webrick sample~~`・・コンテナの作成と起動、ポートの接続、名前つけ、sample〜はイメージ名
- 停止・・コマンド＋C、`$docker container stop`
- 削除・・`$docker container rm コンテナ名`
- ログ・・　`$docker container logs コンテナ名`
- コマンド実行`$docker container exec コンテナ名 やりたいコマンド`
- 後片付け　`$docker system prune -a`使っていないものが一気に消える。  
[![Image from Gyazo](https://i.gyazo.com/82e624954151f34fed196e326b46278c.png)](https://gyazo.com/82e624954151f34fed196e326b46278c)


## Dockerfileを作ろう
```
#Dockerfile
FROM ruby:2.7 #イメージのベース

#どこで作業をするかを指定
WORKDIR /var/www　

#./srcを/var/wwwにコピーする（アプリケーションをDockerにコピー）
COPY ./src /var/www　

# RUN・・ライブラリのインストールを指示する
RUN bundle config --local set path 'vendor/bundle' \
  && bundle install

# コンテナの起動と同時にサーバーの立ち上げを指示する（記述の仕方に注意）
CMD ["bundle", "exec", "ruby", "app.rb"]
```
手順
1. コンテナを起動  
`$ docker image build -t sample/sinatora:latest .`  
`$ docker container run -it -p 4567:4567 --name sinatora -v ${PWD}/src:/var/www sample/sinatora:latest`
- -it・・インタラクティブモードを指定　
- -v・・volumeを指定することで同期するイメージ  

２. コンテナ内の環境を作る  
`bundle config --local set path 'vendor/bundle'`で設定を作り`$bundle install`してapp.rbを記述して`$bundle exec ruby app.rb`で起動させると表示される。 

＊bundle exec ・・Gemfileを読み込んで実行する。 

3. うまくいけば、Dockerfileに記述する。

## Docker ComposeでRailsを構築しよう
### Docker Compose
複数のアプリケーションをまとめて操作
### 基本操作
-  イメージをビルド
`$ docker-compose buile`
-  コンテナの作成と起動
`$ docker-compose up`(-dでバックグラウンドで実施できる)
-  コンテナの停止・削除
`$ docker-compose down`
- コンテナの一覧
`$ docker-compose ps`
- ログの表示
`$ docker-compose logs`
- コンテナを作成してコマンド実行
`$ docker-compose run <サービス><コマンド>`
- 起動中のコンテナにコマンド実行
`$ docker-compose exec <サービス><コマンド>`
### docker-compose.ymlを解説
```
version: '3'
services:
  db:
    image: mysql:8.0
    command: --default-authentication-plugin=mysql_native_password　#MYSQL8.0の設定に必要なもの
    volumes:
      - ./src/db/mysql_data:/var/lib/mysql　
    environment:
      MYSQL_ROOT_PASSWORD: password
  web:
    build: .　
    command: bundle exec rails s -p 3000 -b '0.0.0.0'　#railsのサーバーを起動
    volumes:
      - ./src:/app
    ports:
      - "3000:3000"
    environment:
      RAILS_ENV: development
    depends_on:
      - db　　　　　　　　　　　#dbを依存させている。
```
### 初期設定
- `$ docker-compose run web rails new . --force --database=mysql`・・railsの雛形が作成される
- `$docker-compose build`新しいgemfileができたのでイメージの作り直し
- database.ymlを修正
- `$docker-compose run web rails db:create`webでデータベースを作成する

### 起動
- `$docker-compose up`
## 本番環境に公開しよう
　　1〜3省略
 ### 4.　DB追加
 - `$heroku addons:create cleardb:ignite -a <appname>`・・herokuではDBをアドオンで追加する。（igniteは無料のもの）
 - 環境変数を設定（database.ymlのproductionを変更する）
 - 環境変数をherokuへ追加する（`$ heroku config:add ~~`）

 ### 5.　Dockerfileを本番環境用に修正
 - start.sh作成
 - Dockerfile編集
 - start.sh書き込み
 - コンパイルの環境変数を追加
 - herokuの読み込み時間変更
 - サーバー削除、pidsの削除（エラー防止）
 
 ### 6.　Dockerイメージをビルド・リリース
 - `$ heroku container:push web -a <appname>`・・herokuのコンテナを作成、プッシュ
 - `$ heroku container:release web -a <appname>`・・heroku上にコンテナをリリース
 - `$ heroku run bundle exec rake db:migrate RAILS_ENV=production -a <appname>`・・`$ heroku run ~` で実行したいコマンドを使える。
 - `$ heroku open`


 ### 7.　機能追加
 - ローカルでビューを作る
 - Dockerイメージをビルド、リリースすることで更新


## CI/CDを構築しよう
### GitHub
省略
### CI・・プッシュされるたびにテストが実行され、品質が保たれるようになる。
1. テストコードを記載
- test/controllerに記載あり。コメントアウトを除いて、`assert: false`にしてテストが落ちるようにする。
- `$ docker compose-up`
- `$ docker-compose exec web bundle exec rake test`・・テストを実行する（失敗する）
2. CircleCIに登録
- サイトで登録
- ここは動画の通りに
3. プロジェクトを登録
- ここも動画通りに
4. configを設定
- .circleci/config.ymlを作成
```
# まずは宣言
version: 2.1
orbs:
  ruby: circleci/ruby@1.1.2
  heroku: circleci/heroku@1.2.3
# 
jobs:
  build:
    docker:
      - image: circleci/ruby:2.7
    working_directory: ~/rails-docker-kyt/src　#ここはGitHubのリポジトリ名に揃える。
    # jobの中身を記述
    steps:
      - checkout:
          path: ~/rails-docker-kyt
      - ruby/install-deps

  test:
    docker:
      - image: circleci/ruby:2.7
      - image: circleci/mysql:5.5
        environment:
          MYSQL_ROOT_PASSWORD: password
          MYSQL_DATABASE: app_test
          MYSQL_USER: root
    environment:
      BUNDLE_JOBS: "3"
      BUNDLE_RETRY: "3"
      APP_DATABASE_HOST: "127.0.0.1"
      RAILS_ENV: test
    working_directory: ~/rails-docker-kyt/src
    steps:
      - checkout:
          path: ~/rails-docker-kyt
      - ruby/install-deps
      - run:
          name: Database setup
          command: bundle exec rails db:migrate
      - run:
          name: test
          command: bundle exec rake test

  # ここは次のCDの時に記述する。
  deploy:
    docker:
      - image: circleci/ruby:2.7
    steps:
      - checkout
      - setup_remote_docker:
          version: 19.03.13
      - heroku/install
      - run:
          name: heroku login
          command: heroku container:login
      - run:
          name: push docker image
          command: heroku container:push web -a $HEROKU_APP_NAME
      - run:
          name: release docker image
          command: heroku container:release web -a $HEROKU_APP_NAME
      - run:
          name: database setup
          command: heroku run bundle exec rake db:migrate RAILS_ENV=production -a $HEROKU_APP_NAME
# ここで順番を指定
workflows:
  version: 2
  build_test_and_deploy:
    jobs:
      - build
      - test:
          requires:
            - build
      - deploy:
          requires:
            - test
          filters:
            branches:
              only: main
```
5. 環境変数を設定
- 動画通り
6. GitHubにプッシュ
- 動画見て、テストが落ちていることを確認
7. テストを修正
- `$ assert true`にする。これで成功

### CD・・mainにマージされた時に自動でデプロイされるようになる。
1. configを修正
- 動画見て
- `branches:only: main`でmainブランチにマージされた時のみデプロイされるようになる。
2. 環境変数を設定
- 動画見て
3. Viewファイルを修正
- 動画見て
4. GitHubにプッシュ
- マージしたらデプロイされているか確認
5. マージ、デプロイ
- ここでのデバックの方法は参考になるので注目。

