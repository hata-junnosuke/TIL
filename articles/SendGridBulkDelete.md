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

そのため、大量削除の場合は次のように工夫が必要です。

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

