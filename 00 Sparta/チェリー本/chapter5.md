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
## ハッシュとキーワード引数について詳しく
### 使用頻度が高いメソッド
- keys
- values
- has_key? / key? / include? / member?
```
irb(main):001:0> currencies = { japan: 'yen', us: 'doller' } 
=> {:japan=>"yen", :us=>"doller"}
irb(main):002:0> currencies.keys
=> [:japan, :us]
irb(main):003:0> currencies.values
=> ["yen", "doller"]
irb(main):004:0> currencies.has_key?(:japan)
=> true
irb(main):005:0> currencies.has_key?(:itary)
=> false
```

### **でハッシュを展開させる
```
irb(main):001:0> currencies = { us: 'doller', india: 'rupee' } 
=> {:us=>"doller", :india=>"rupee"}
irb(main):002:0> { japan: 'yen', **currencies }
=> {:japan=>"yen", :us=>"doller", :india=>"rupee"}
```
- mergeでも同じことができる
```
irb(main):003:0> { itary: 'euro' }.merge(currencies)
=> {:itary=>"euro", :us=>"doller", :india=>"rupee"}
```

### 擬似キーワード引数

### **引数
```
irb(main):001:1* def buy_burger(menu, drink: true, poteto: true)
irb(main):002:0> end
=> :buy_burger
irb(main):003:0> buy_burger(menu, drink: true, poteto: true, salad: false)
irb(main):004:0> buy_burger('fish', drink: true, poteto: true, salad: false)
(irb):1:in `buy_burger': unknown keyword: :salad (ArgumentError) #エラーになる     
        from (irb):4:in `<main>'
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/lib/ruby/gems/3.0.0/gems/irb-1.3.5/exe/irb:11:in `<top (required)>'
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/bin/irb:23:in `load'
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/bin/irb:23:in `<main>'
irb(main):005:1* def buy_burger(menu, drink: true, poteto: true, **others)　#エラーにならない
irb(main):006:1*   puts others
irb(main):007:0> end
=> :buy_burger
irb(main):009:0> buy_burger('fish', drink: true, poteto: true, salad: false)
{:salad=>false}   # ハッシュにキーワードがっ！！
=> nil
```

### ハッシュを明示的にキーワード引数に変換する＊＊
キーワードについては再学習することにする。

### メソッド呼び出し時の{]の省略
- 最後がハッシュであれば{}は省略可能
```
irb(main):001:0> b = ['fish', drink: true, poteto: false]
=> ["fish", {:drink=>true, :poteto=>false}]
irb(main):002:0> b[1]
=> {:drink=>true, :poteto=>false} #drink: trueだけでなくハッシュでまとめて呼び出される。{}なくても
```
### ハッシュリテラルの{}とブロックの{}
- ややこしいが、メソッドの第1引数にハッシュを渡すときは（）が必要。逆に第2引数以降はいらない。（いつ使うのか？）
```
irb(main):001:0> puts ({ foo: 1, bar: 2 })
{:foo=>1, :bar=>2}
=> nil
irb(main):002:0> puts { foo: 1, bar: 2 }
/Users/hatajunnosuke/.rbenv/versions/3.0.3/lib/ruby/3.0.0/irb/workspace.rb:116:in `eval': (irb):2: syntax error, unexpected ':', expecting '}' (SyntaxError)
```

### ハッシュから配列へ
- to_aで配列へ
- to_hでハッシュへ
```
irb(main):002:0> currencies = { japan: 'yen', usa: 'doller' }
=> {:japan=>"yen", :usa=>"doller"}
irb(main):003:0> currencies.to_a
=> [[:japan, "yen"], [:usa, "doller"]]
irb(main):004:0> array = [[:japan, "yen"], [:usa, "doller"]]
=> [[:japan, "yen"], [:usa, "doller"]]
irb(main):005:0> array.to_h
=> {:japan=>"yen", :usa=>"doller"}
```
### ハッシュのデフォルト値を理解する
- ハッシュでnil以外のデフォルト地を指定したくなったらここを読め！！（再学習がよし）

### その他〜
ここはあとででOK

## シンボルについてもっと詳しく
### シンボル作成方法

### %記法
- %Iで式展開と改行文字も含めるようになる

### シンボルと文字列の関係

## コラム
- メソッド定義時の引数の順番
- &.演算子（nilならばnilで返す。エラーにはならない）
- ||=(変数にnil以外を入れておきたい時に使う)
- !!（trueかfalseを帰えさせる）
