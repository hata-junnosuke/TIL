# ハッシュやシンボルを理解する
## ハッシュ
- キーと値の組み合わせでデータを管理するオブジェクト。連想配列やディクショナリ、マップとも呼ばれるらしい。

### 要素の追加、変更、取得
- 要素の追加
```
irb(main):001:0> currencies = { 'japan' => 'yen' }
=> {"japan"=>"yen"}
irb(main):002:0> currencies['itary'] = 'euro'
=> "euro"
irb(main):003:0> currencies
=> {"japan"=>"yen", "itary"=>"euro"}
```
- 取り出し方
```
irb(main):004:0> currencies['japan']
=> "yen"
```
- 存在しないキーを指定するとnil
```
irb(main):005:0> currencies['usa']
=> nil
```
### ハッシュを使った繰り返し処理
```
irb(main):006:1* currencies.each do |key, value|
irb(main):007:1*   puts "#{key} : #{value}"
irb(main):008:0> end
japan : yen
itary : euro
=> {"japan"=>"yen", "itary"=>"euro"}
```
### ハッシュの同値比較、要素数の取得、要素の削除
```
irb(main):001:0> a = { 'x' => 1, 'y' => 2 }
=> {"x"=>1, "y"=>2}
irb(main):002:0> b = { 'y' => 2, 'x' => 1 }  
=> {"y"=>2, "x"=>1}
#順番違ってもキーと値が同じならtrue
irb(main):003:0> a == b
=> true
# sizeで要素の個数
irb(main):004:0> a.size
=> 2
# deleteで削除
irb(main):005:0> a.delete('x')
=> 1
irb(main):006:0> a
=> {"y"=>2}
```
## シンボル
### 特徴と主な用途
- 表面上は文字列っぽいのでプログラマにとって理解しやすい
- 内部的には整数なので、コンピュータは高速に値を比較できる
- 同じシンボルは同じオブジェクトなのでメモリの使用効率が良い
- イミュータブルなので勝手に値を変えられる心配がない

## 続・ハッシュについて
- キーワード引数とハッシュ

## 例題
```
UNITS = { m: 1.0, ft: 3.28, in: 39.37 }

def convert_length(length, from: :m, to: :m)
  (length / UNITS[from] * UNITS[to]).round(2)
end
```
