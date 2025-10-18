## 💬 はじめに

Railsアプリで環境変数を管理するとき、dotenv の gem を使って .env ファイルに書いている方、多いと思います。
でも、環境が増えるほど `.env.development`, `.env.staging`, `.env.production` ... とファイルが増えて、
共有や更新がどんどん面倒になっていきませんか？

私もまさにその状態でした。

「**このキー、最新？**」「**本番の.envはどこ？**」といったカオスを一掃したくて、
Rails標準の `credentials.yml.enc` に移行しました。

結果的に、**マスターキー1つを共有するだけで環境変数が全て管理できる**ようになりました。
この記事では、その移行手順とメリットを紹介します。

---

## 💡 なぜ `.env` をやめたのか

`.env` は便利ですが、チーム開発では次の課題があります。

| 課題     | 内容                      |
| ------ | ----------------------- |
| ファイル共有 | 環境ごとに `.env` を共有する必要がある |
| 差分管理   | Gitにコミットできないため履歴が追えない   |
| セキュリティ | 各環境での転送・共有にリスクがある       |

一方、Railsの `credentials` はこれらの問題を**すべて標準機能で解決**できます。

---

## 🔐 Rails Credentialsとは

Rails 5.2以降に導入された**暗号化済み設定ファイル**機能です。

* 設定は `config/credentials.yml.enc` に保存
* 編集時は自動で暗号化・復号される
* **マスターキー (`config/master.key`) だけで復号可能**
* 環境ごとに分離もできる（例：`config/credentials/production.yml.enc`）

つまり、**Gitに安全にコミットできて、チーム全体で一貫した設定管理ができる**仕組みです。

---

## 🛠️ 実際の移行手順

### 1. 既存の `.env` の内容を確認

```bash
cat .env
```

例：

```bash
DATABASE_USERNAME=root
DATABASE_PASSWORD=password
AWS_ACCESS_KEY_ID=xxxx
AWS_SECRET_ACCESS_KEY=yyyy
```

---

### 2. credentials を開く

```bash
bin/rails credentials:edit
```

（初回実行時に `config/master.key` と `config/credentials.yml.enc` が生成されます）

---

### 3. `.env` の内容を移す

```yaml
aws:
  access_key_id: xxxx
  secret_access_key: yyyy

database:
  username: root
  password: password
```

保存すると、自動的に暗号化されます。

---

### 4. コードから呼び出す

`.env`の代わりに、`Rails.application.credentials`を使います。

```ruby
# 例: S3クライアント設定
Aws::S3::Client.new(
  access_key_id: Rails.application.credentials.dig(:aws, :access_key_id),
  secret_access_key: Rails.application.credentials.dig(:aws, :secret_access_key)
)
```

---

### 5. 環境ごとに分離したい場合

Rails 6以降なら環境別に管理できます。

```bash
bin/rails credentials:edit --environment production
```

生成されるファイル：

```
config/credentials/production.yml.enc
config/credentials/development.yml.enc
```
#### ちょっとした小技
私は、環境ごとにファイルを分けずに、1つの credentials にまとめて管理しています。
こんな感じで、環境名をトップレベルキーにして記載しています👇
```
development:
  frontend_url: http://localhost:8000
production:
  frontend_url: https://example.jp
  database_url: mysql2://admin:hogehoge-db@hogehoge-db.1223334444.ap-northeast-1.rds.amazonaws.com
test:
  frontend_url: http://localhost:8000
```
呼び出すときは、Rails.env を動的に参照することで環境に応じた値を取得できます。
```
Rails.application.credentials.dig(Rails.env.to_sym, :frontend_url) 
```
こうすることで一つのファイルであらゆる環境に対応をまとめることができます！！

## 🚀 運用のポイント

* **マスターキー (`config/master.key`) は共有が必要**

  * **Gitには含めてはいけません！**
* 環境変数としてセットも可能

  ```bash
  export RAILS_MASTER_KEY=xxxxxxxxxxxx
  ```
* CI/CD環境では `RAILS_MASTER_KEY` を設定するだけで動作

---

## ✅ 移行して感じたメリット

| 項目     | `.env`時代 | credentials移行後 |
| ------ | -------- | -------------- |
| ファイル共有 | 手動共有     | master.keyのみ共有 |
| セキュリティ | ファイル平文   | AES暗号化         |
| Git管理  | 除外対象     | 安全にコミット可能      |
| デプロイ   | ファイル配置必要 | 環境変数1つで完結      |
| 可読性    | 環境ごとに散在  | 一元管理           |

個人的には「**マスターキーひとつで全て管理できる安心感**」が一番大きいです。
本番環境に`.env`を転送するような手間もなくなりました。

---

## 🧭 まとめ

`.env`は手軽、でもスケールしにくい。
`credentials`は最初のハードルは少し高いけど、**チーム開発・セキュリティ・CI/CD対応**に強い。

Railsが標準で用意している以上、是非とも使いましょう！！

もしあなたのプロジェクトが `.env` で管理しているなら、
**「マスターキーひとつで管理する世界」** に移行してみてください。
きっと開発がシンプルになります。

---

## 🧰 参考

* [Railsガイド: Rails の Credentials](https://railsguides.jp/security.html#%E8%A8%AD%E5%AE%9A%E3%81%AE%E6%9A%97%E5%8F%B7%E5%8C%96)
* [パーフェクト Ruby on Rails　【増補改訂版】](https://www.amazon.co.jp/%E3%83%91%E3%83%BC%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88-Ruby-Rails-%E3%80%90%E5%A2%97%E8%A3%9C%E6%94%B9%E8%A8%82%E7%89%88%E3%80%91-%E3%81%99%E3%81%8C%E3%82%8F%E3%82%89-%E3%81%BE%E3%81%95%E3%81%AE%E3%82%8A-ebook/dp/B08D3DW7LP/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2G5BWZQEOOOLT&dib=eyJ2IjoiMSJ9.ZwseYYWTIJ2aQ9QiIRBykw.lRabUJ6b6WgEHdw6YA5buV9BbbR0Mx3Ss1Uotu6LwAo&dib_tag=se&keywords=%E3%83%91%E3%83%BC%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88Rails&qid=1760694870&sprefix=%E3%83%91%E3%83%BC%E3%83%95%E3%82%A7%E3%82%AF%E3%83%88rails%2Caps%2C187&sr=8-1)
