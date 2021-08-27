# まとめて扱う-配列
## 要素を追加・削除する
### 要素を追加する
- pushメソッド、　<< で配列末尾に要素を追加、unshiftメソッドで配列先頭に要素を追加する。
[![Image from Gyazo](https://i.gyazo.com/14a06642b628802fa9482591ae63dcf6.png)](https://gyazo.com/14a06642b628802fa9482591ae63dcf6)
### 要素を削除する
- popメソッドで配列末尾の要素を削除、shiftメソッドで配列先頭の要素を削除する。なお、削除した要素を返すこともできる。(firstとlastでは配列から要素を削除しない点で異なる。)
[![Image from Gyazo](https://i.gyazo.com/350b876a886b59a6fcc6555a3f829390.png)](https://gyazo.com/350b876a886b59a6fcc6555a3f829390)
### 配列を足し算、引き算する
- 配列の足し算はつながった配列をつくり、引き算は下の配列から引く配列の要素を取り除いた配列をつくる。
[![Image from Gyazo](https://i.gyazo.com/1a2eb296bb20ecde131c9c0d79a6b97c.png)](https://gyazo.com/1a2eb296bb20ecde131c9c0d79a6b97c)
## 配列を繰り返し処理する
### 繰り返しを途中で終わらせる　-break
- breakを使うと繰り返しの処理を途中で終わらせることができる。下ではx == 2になった時点で繰り返しが終了している。
[![Image from Gyazo](https://i.gyazo.com/f25a945a431b5b524a56185348a40f0e.png)](https://gyazo.com/f25a945a431b5b524a56185348a40f0e)
- nextを使うと繰り返しの処理をそこで終わりにして、次の回へ進める。下ではx == 2になった時点で処理を終わらせて、x == 3の処理を実施している。  
[![Image from Gyazo](https://i.gyazo.com/a7e6fa507ce9a3f9f9f65a4f339cf5e5.png)](https://gyazo.com/a7e6fa507ce9a3f9f9f65a4f339cf5e5)
- 範囲を指定して繰り返す.下の(3..5)はRangeオブジェクトという範囲を表すオブジェクトでこれと組み合わせることで範囲の中で繰り返しができる。   
[![Image from Gyazo](https://i.gyazo.com/5eeb48723fbdf9b6ca038fa81407a248.png)](https://gyazo.com/5eeb48723fbdf9b6ca038fa81407a248)
## 演習問題
- 11は何も起こらないで正解。
[![Image from Gyazo](https://i.gyazo.com/0a1076d057615bb9d575c2ae2cb4bf8d.png)](https://gyazo.com/0a1076d057615bb9d575c2ae2cb4bf8d)
