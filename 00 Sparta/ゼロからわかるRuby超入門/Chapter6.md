# 組で扱う　ー　ハッシュ
## オブジェクトを組で扱う
### ハッシュとは
ハッシュは複数のオブジェクトをまとめることができる入れもの。ハッシュは「キー」と「値」のセットでデータを扱うことができる。
### シンボルとは
`{:coffee => 300, :caffe_latte => 400}`
キーの部分の`:coffee`　`:caffe_latte`はシンボルと呼ばれるオブジェクトである。  
文字列からシンボルはto_symメソッド、シンボルから文字列はto_sメソッドで変換できる。
### ハッシュの書き方
```
{:coffee => 300, :caffe_latte => 400}
{coffee: 300, caffe_latte: 400}
```
### ハッシュは代入で名前をつけられる
`menu = {coffee: 300, caffe_latte: 400}`
### ハッシュから値を取得する
[![Image from Gyazo](https://i.gyazo.com/b3b687a21f7bc176e314c28c51810f16.png)](https://gyazo.com/b3b687a21f7bc176e314c28c51810f16)
## キーと値の組を追加・削除する
### ハッシュへキーと値の組みを追加する
[![Image from Gyazo](https://i.gyazo.com/3a0a87e757a6b144c0cf33d2b86a9a24.png)](https://gyazo.com/3a0a87e757a6b144c0cf33d2b86a9a24)
- `ハッシュ名[:キー] = 値` で末尾に追加することができる。
- ハッシュは複数持つことができないので、すでにあるキーと値を追加すると上書きされる。
### 存在しないキーを指定したとき
[![Image from Gyazo](https://i.gyazo.com/4e9b5b3f496aa48f907ed300386e3131.png)](https://gyazo.com/4e9b5b3f496aa48f907ed300386e3131)
- 存在しないキーを指定するとnilが返ってくる。
- defaultでキーがないときの設定をすることができる。
### ハッシュをまとめる、ハッシュからキーと値を削除する
[![Image from Gyazo](https://i.gyazo.com/77b026e3141842c5eb22308d15da894f.png)](https://gyazo.com/77b026e3141842c5eb22308d15da894f)
- mergeメソッドで2つのハッシュを1つにできる。
- deleteメソッドで削除できる。

## ハッシュの要素を繰り返し処理をする
[![Image from Gyazo](https://i.gyazo.com/018d79e701c8ce8daf88ccb2343ee8f2.png)](https://gyazo.com/018d79e701c8ce8daf88ccb2343ee8f2)
- eachメソッドとブロックを使うことで繰り返しができる。

## 演習問題
[![Image from Gyazo](https://i.gyazo.com/648596c09c7e0e3eccea7399077dc967.png)](https://gyazo.com/648596c09c7e0e3eccea7399077dc967)
- #5,#7は再度確認せよ！
