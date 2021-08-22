# かんたんなプログラムを書く
## 計算する
- 計算で小数を出力するには、小数オブジェクト「.0」「.2」などをつけて計算をする（両方でも片方でも可）。整数同士の計算は小数は切り捨てられる。   
[![Image from Gyazo](https://i.gyazo.com/01ad78e2d4416cd5b9be5f04b1b314ad.png)](https://gyazo.com/01ad78e2d4416cd5b9be5f04b1b314ad)
- 累乗と剰余
```
puts 2 ** 8 => 「256」・・2の8乗
puts 7 % 3 => 「1」・・7を3で割った余り。
```
## オブジェクトと変数を理解する
- getsメソッドを使うとターミナル上から値を入力するプログラムを書くことができる。
- キーボードから入力された文字列には”￥n”という改行を表すものが隠れて追加される。これを消すためにはchompメソッドを使う。
[![Image from Gyazo](https://i.gyazo.com/732602e0275d1d3b1b3fb2b04475ede9.png)](https://gyazo.com/732602e0275d1d3b1b3fb2b04475ede9)
[![Image from Gyazo](https://i.gyazo.com/50b6c9c4b35034474c516be992236550.png)](https://gyazo.com/50b6c9c4b35034474c516be992236550)
## プログラムにコメントを書く
- 数行に渡ってコメントをする場合、=biginから=endで囲む。  
[![Image from Gyazo](https://i.gyazo.com/d9312ed84bd3f68c7ae6a9632ba03201.png)](https://gyazo.com/d9312ed84bd3f68c7ae6a9632ba03201)
## プログラムの間違いを見つけて直す
- 問題があって動かない部分をバグといい、その原因を調べて正すことをでバックという。
- pメソッドとputsメソッド
  - pメソッド・・原則デバックの道具に使う。
  - putsメソッド・・プログラムの機能として意図して表示する場合に使う。

## 演習問題　
クリア
