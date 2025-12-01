## 💬 はじめに

SendGrid の Single Sends（マーケティングキャンペーン）には **一括削除 API** が提供されています。

👉 公式ドキュメント
[https://www.twilio.com/docs/sendgrid/api-reference/single-sends/bulk-delete-single-sends](https://www.twilio.com/docs/sendgrid/api-reference/single-sends/bulk-delete-single-sends)

しかしドキュメントには **“任意の ID を選択して削除する方法”** が明確に書かれていません。
**“任意の ID を選択して削除する方法”** はできるのでしょうか？

## ✅ 結論

SendGrid の PHP ライブラリ [php-http-client](https://github.com/sendgrid/php-http-client) では、

### **`delete(null, ['ids' => [...]]);` と書けば、指定した複数 ID（最大 50件）だけを削除できる。**

* **第1引数：body（今回は null）**
* **第2引数：クエリパラメータ（ids を入れる）**

これは **php-http-client 側の `delete()` の定義**によるものです。

## 📘 SendGrid 公式のサンプル

SendGrid 公式のサンプルは以下の形です：

```php
$response = $sg->client
    ->marketing()
    ->singlesends()
    ->delete();
```

これを使って “選択削除” を行うには、
**delete の第1引数と第2引数を正しく使う必要があります。**

## 🛠️ 選択削除を実装
今回の選択可能となる鍵は `php-http-client` のこの定義：

👉 [https://github.com/sendgrid/php-http-client/blob/3002e9c8d21dcf664936ced4e5802ba8581a52c2/lib/Client.php#L21](https://github.com/sendgrid/php-http-client/blob/3002e9c8d21dcf664936ced4e5802ba8581a52c2/lib/Client.php#L21)

```php
* @method Response delete($body = null, $query = null, $headers = null, $retryOnLimit = null)
```

つまり、選択可能にするには：

| 引数       | 意味           | 今回の用途                  |
| -------- | ------------ | ---------------------- |
| **第1引数** | body         | `null` を渡す             |
| **第2引数** | query  | `['ids' => [...]]` を渡す |

↓コードにすると以下のようになります！
```php
<?php
// require 'vendor/autoload.php'; // Composer 使用時
// require_once '<PATH TO>/sendgrid-php.php'; // 未使用時

$apiKey = getenv("SENDGRID_API_KEY");
$sg = new \SendGrid($apiKey);

// 削除したい Single Send の ID 達
$idsToDelete = [
    "single-send-id-123",
    "single-send-id-456",
    "single-send-id-789",
];

try {
    $response = $sg->client
        ->marketing()
        ->singlesends()
        // ★第1引数は body（null）、第2引数に query として ids を渡す
        ->delete(
            null,                   // body
            ['ids' => $idsToDelete] // query
        );

    echo $response->statusCode() . "\n";
    print_r($response->headers());
    echo $response->body() . "\n";

} catch (Exception $ex) {
    echo "Caught exception: " . $ex->getMessage();
}
```

## 📌 いつ使えるのか？

Single Sends は放置すると

* キャンペーン一覧が重くなる
* API 応答が遅くなる
* メールが送信されなくなる（実際に発生し得る）
* 管理画面の UI が詰まる

といった問題につながります。

そのため、**定期的に古いキャンペーンを削除するバッチ処理**は非常に有効です。

例えば：

* 状態が "draft" のまま古いもの
* 過去に送信済みで再利用されないもの
* 作成日が 90 日以上前のもの

などをルール化して削除することで、SendGrid の負荷や UI・API の不具合を事前に防げます。

## ⚠️ 注意：Bulk-delete の最大件数は 50 件

公式仕様では、

### → **1 回で削除できる Single Sends は最大 50 件**

そのため、大量削除の場合は次のように繰り返しを活用する必要があります。

```php
$chunks = array_chunk($allSendIds, 50);

foreach ($chunks as $chunk) {
    $sg->client
        ->marketing()
        ->singlesends()
        ->delete(null, ['ids' => $chunk]);

    sleep(1); // 任意：レート制限対策
}
```

これで安全に大量キャンペーンを削除できます。

# 👀 まとめ
SendGrid の Single Sends に対して「選択した ID だけをまとめて削除」する方法は、公式ドキュメントでは明確に触れられていません。しかし、SendGrid のライブラリである php-http-client の実装を確認すると、

- `delete(null, ['ids' => [...]]);`という呼び出し方で 任意の複数 ID を選択して削除できる
- bulk-delete API の **上限 50 件**までの制限がある
- キャンペーンが溜まりすぎた場合の不具合（送信不能・UI の遅延など）を防ぐため、バッチ処理で定期削除する運用は非常に有効

といった点が明確になります。

実務では “古いキャンペーンの自動クリーンアップ” によってSendGrid 全体の安定稼働につながるため、知っておくと便利なテクニックです。
使用する場面は少ないかもしれませんが、この記事が困っている方の助けになれば嬉しいです！

## 📚 参考資料
🔗 SendGrid 公式ドキュメント

Single Sends Bulk Delete（API Reference）
https://www.twilio.com/docs/sendgrid/api-reference/single-sends/bulk-delete-single-sends

🔗 PHP ライブラリ

php-http-client
https://github.com/sendgrid/php-http-client/blob/main/lib/Client.php
