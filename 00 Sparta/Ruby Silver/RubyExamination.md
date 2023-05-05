#  RubyExamination
## 問題
```
def hoge
  x = 0
  1.step(5,1) do |i|
    x += 1
  end
  puts x
end
hoge
```
### 解説
Numeric#step(limit, step)はselfからstepずつ加算し、limitまでをブロックに渡します。

問題の1.step(5,1)は「1から1ずつ加算し、5までの数値」という意味になります。
```
irb(main):092:1* def hoge
irb(main):093:1*   x = 0
irb(main):094:2*   1.step(10,1) do |i|
irb(main):095:2*     x += 2
irb(main):096:1*   end
irb(main):097:1*   puts x
irb(main):098:0> end
irb(main):099:0> hoge
20
=> nil
irb(main):100:1* def hoge
irb(main):101:1*   x = 0
irb(main):102:2*   1.step(5,1) do |i|
irb(main):103:2*     x += 2
irb(main):104:1*   end
irb(main):105:1*   puts x
irb(main):106:0> end
irb(main):107:0> hoge
10
=> nil
irb(main):108:1* def hoge
irb(main):109:1*   x = 0
irb(main):110:2*   1.step(10,5) do |i|
irb(main):111:2*     x += 2
irb(main):112:1*   end
irb(main):113:1*   puts x
irb(main):114:0> end
irb(main):115:0> hoge
4
=> nil
```

## 問題(Valuesメソッド)
```
hash = {price: 100, order_code: 200, order_date: "2018/09/20", tax: 0.8}

p hash.__(1)__
```
### 解説
期待値はキーが:priceと:taxの値を配列です。

Hash#key Hash#keyメソッドは引数を１つ取ります。返却値は引数に合った値を持つキーです。 もし引数の値を持つキーがなければ、nilを返します。 ```ruby hash = {price: 100, order_code: 200, order_date: "2018/09/20", tax: 0.8}
p hash.key 100 # :price が表示される
p hash.key 150 # nil が表示される


- `Hash#values`
`Hash#values`メソッドは引数を取りません。返却値はレシーバーの値を集めた配列です。
```ruby
hash = {price: 100, order_code: 200, order_date: "2018/09/20", tax: 0.8}

p hash.values # [100, 200, "2018/09/20", 0.8] が表示される
Hash#values_at Hash#values_atメソッドは可変長引数を取ります。返却値は引数に指定したキーの値を集めた配列です。 ```ruby hash = {price: 100, order_code: 200, order_date: "2018/09/20", tax: 0.8}
p hash.values_at(:price, :tax) # [100, 0.8] が表示される


- `Hash#[]`
`Hash#[]`メソッドは引数を１つ取ります。返却値は引数に合ったキーが持つ値です。  
もし引数のキーがハッシュになければ、`nil`を返します。
```ruby
hash = {price: 100, order_code: 200, order_date: "2018/09/20", tax: 0.8}

p hash.[](:price) # 100 が表示される
p hash.[](:bazz) # nil が表示される
この問題では、複数のキーを指定し対応する値を配列にするvalues_at(:price, :tax)が正解です。
```
## 問題(invertメソッド)
```
h = {a: 100, b: 100}
puts h.invert
```
### 解説
Hash#invertはキーと値を入れ替えます。
入れ替えの結果キーが重複した場合は、後に定義された方が優先されます。

Hash#invertの例
```
p ({a: 100, b: 100}).invert
# 実行結果
# {100=>:b}
```
## 問題
```
a1 = [1,2,3]
a2 = [4,2,3]

p a1 & a2
```
### 解説
Array#&は論理積(AND)を求めることができます。
今回の問題では、[1, 2, 3]と[4, 2, 3]に双方に存在する[2, 3]が出力されます。
## 問題
```
d = Date.new(2015, 1, 5)
puts d.strftime(XXXX)
# 出力結果
01/05/15
```
### 解説
この問題の回答は"%x"
フォーマット文字列	意味
%x	日付(%m/%d/%y)
%m	月を表す数字(01-12)
%M	分(00-59)
%d	日(01-31)
%D	日付(%m/%d/%y)
%y	西暦の下2桁(00-99)
%Y	西暦を表す数(9999)

## 問題(any?メソッド)
```
$val = 0

class Count
  def self.up
    $val = $val + 1
    $val == 3 ? true : false
  end
end

[*1..10].any? do
  Count.up
end

p $val
```
### 解説
Enumerable#any?はブロックの戻り値がtrueになると繰り返しをその時点で止めます。
繰り返しが止まるのは3回目の繰り返し、つまり$valが3になった時点です。

この問題のプログラムを実行すると、3が表示されます。
## 問題(...と..)
```
str = "abcdefgh"
puts str[4..6]
```
### 解説
puts "abc"[0..2] # => abc
puts "abc"[0...2] # => ab
puts "abc"[0..-2] # => ab
## 問題(ハッシュのメソッド)
```
省略
```
### 解説
空のHashオブジェクトを生成するにはHash({}), {}, Hash.newのいずれかを用います。
Hash#to_hは2次元配列からハッシュを生成します。例えば[[1, "data 1"], [2, "data 2"]].to_hの結果は{1=>"data 1", 2=>"data 2"}になります。
Hash#mergeはselfと引数のハッシュをマージし、新しいHashを返します。Hash#mergeは非破壊的メソッドです。
```
h1 = { "a" => 100, "b" => 200 }
h2 = { "b" => 246, "c" => 300 }
h3 = { "b" => 357, "d" => 400 }
h1.merge          #=> {"a"=>100, "b"=>200}
h1.merge(h2)      #=> {"a"=>100, "b"=>246, "c"=>300}
h1.merge(h2, h3)  #=> {"a"=>100, "b"=>357, "c"=>300, "d"=>400}
```
Hash#clearはselfより要素を全て取り除きます。Hash#clearは破壊的メソッドです。
## 問題(indexメソッド)
```
a = "Ruby"
b = " on Rails"
a.concat b
a.reverse!
p a.index("R", 1)
```
### 解説
String#index(pattern, pos)	文字列のpos番目からpatternを検索し、最初に見つかった位置を返します
問題のコードを1行ずつ実行すると以下のようになります。
```
irb(main):143:0> a = "Ruby"
=> "Ruby"
irb(main):144:0> b = " on Rails"
=> " on Rails"
irb(main):145:0> a.concat b
=> "Ruby on Rails"
irb(main):146:0> a.reverse!
irb(main):147:0> p a.index("R", 1)
4
=> 4
irb(main):148:0> p a
"sliaR no ybuR"
=> "sliaR no ybuR"
```
## 問題(attr_writer)
```
class Foo
  attr_writer :a
end

foo = Foo.new
foo.a = "REx"
puts foo.a
```
### 解説
attr_writerメソッドは引数に指定した名前のインスタンス変数に対して書き込み用メソッド(setterメソッド)を定義します。

引数にはStringクラスかSymbolクラスのリテラルを指定ます。複数指定することも可能です。

クラス内で例1のようにattr_writer :aと記述すると例2のようにインスタンス変数@aに対して書き込み用のメソッドを定義したことになります。

例1, 例2のFooクラスはそれぞれインスタンス変数@aに対しての書き込み用メソッドを持ちます。

書き込み用のメソッドなので、読み取りはできません。読み取り用のメソッドが定義されていないインスタンス変数を参照しようとするとNoMethodErrorが発生します。

例1
```
class Foo
  attr_writer :a
end

foo = Foo.new
foo.a = "value" # => オブジェクトfooが保持するインスタンス変数@aに文字列"value"が代入される
p foo.a         # => インスタンス変数@aに対して読み取り用のメソッドが定義されていないためNoMethodErrorが発生
```
例2
```
class Foo
  def a=(val)
    @a = val
  end
end
```

## 問題(delete_if)
```
Arrayクラスでdelete_ifと同じ動作をするメソッドを選びなさい

```
### 解説
Array#delete_ifは1要素ずつブロックへ渡し、その結果が真の要素をselfから削除します。
```
a = [0, 1, 2, 3, 4, 5]
a.delete_if{|x| x % 2 == 0}
p a #=> [1, 3, 5]
```

Array#delete_ifと同じ動作をするのはreject!です。
Array#delete_ifとの違いは1要素以上削除されれば、selfを返します。それ以外は、nilを返します。

その他の選択肢のメソッドの意味を以下にまとめます。

メソッド	説明
delete	引数で渡された値と等しい要素をselfより取り除きます
reject	ブロックの結果が偽の要素を集めた新しい配列を返します
```
# 偶数を除外する (奇数を集める)
(1..6).reject {|i| i % 2 == 0 } # => [1, 3, 5]
```

## 問題(pop)
```
s = ["one", "two", "three"]
s.pop
s.pop
s.unshift
s.push "four"
p s
```
### 解説
1: s = ["one", "two", "three"]
2: s.pop
3: s.pop
4: s.unshift
5: s.push "four"
6: p s
```
#<実行結果>
#1: ["one", "two", "three"]
#2: ["one", "two"]
#3: ["one"]
#4: ["one"]
#5: ["four", "one"]
```
## 問題(flatten)
```
省略
```
### 解説
Array#flattenはselfを再帰的に平坦化します(3次元配列を1次元配列の配列を返す)。
Array#flatten!は破壊的にselfを再帰的に平坦化します。平坦化が行われない場合は、nilを返します。

String#reverseはselfを逆順にした配列を返します。

%||は%記法といいます。文字列リテラル等を定義することができます。
詳しい説明は%記法を参照してください。
```
# 自身を再帰的に平坦化する例。
a = [1, [2, 3, [4], 5]]
p a.flatten                     #=> [1, 2, 3, 4, 5]
p a                             #=> [1, [2, 3, [4], 5]]
```
## 問題(inject,abs,abs2,succ、演算子|,&)

XXXXに記述した場合に実行結果が30以上になるプログラムを選択してください。
```
a = [-1, 2, 3, 4, 5]
b = (4..6).to_a

puts XXXX
```
### 解説
```
irb(main):174:0> a = [-1, 2, 3, 4, 5]
irb(main):175:0> b = (4..6).to_a
=> [4, 5, 6]
irb(main):176:0> puts a.inject(:+) + b.inject(:+)
28
=> nil
irb(main):177:0> puts (a | b).inject(:-).abs + (a & b).inject(:+).abs
irb(main):178:0*
30
=> nil
irb(main):179:0> puts (a | b).inject(:*) + b.inject(0) { |x, y| x + y ** 3 }
-315
=> nil
irb(main):180:0> puts ((a || b).map(&:succ).inject(:*) * (a && b).inject(:*).abs2 + 29)
29
=> nil
```
Enumerable#injectメソッドは、レシーバの要素に対して引数で指定した処理を行います。

Enumerable#mapメソッドは、レシーバの各要素に対して引数で指定した処理を行なった結果を含む配列を返します。
```
[1, 2, 3].inject(:+)                 # => 6
[1, 2, 3].inject(0) { |x, y| x + y } # => 6
# 自乗和を計算する。初期値をセットする必要がある。
p [2, 3, 4, 5].inject(0) {|result, item| result + item**2 }  #=> 54
```
Fixnum#absメソッドは、絶対値を返します。

Numeric#abs2メソッドは、絶対値の2乗を返します。

Integer#succメソッドは、次の整数を返します。
演算子|は、集合の和演算を返し、&は積演算を返します。
```
["a", 1] | ["b", 2]         # => ["a", 1, "b", 2]
["a", 1] & ["a", "b", 1, 2] # => ["a", 1]
```
論理演算子||(論理和)と&&(論理積)は左辺の式で結果が決まる場合、右辺の評価は行わず、最後に評価した値を返します。
```
1 || nil # => 1 右辺は評価されない
nil || 1 # => 1
1 && nil # => nil
nil && 1 # => nil 右辺は評価されない
```
## 問題(strftime)
```
irb(main):184:0> Date.today.strftime("%F")
=> "2023-05-05"
```
### 解説

フォーマット文字列	意味
%F	日付(%Y-%m-%d)
%Y	西暦4桁
%y	西暦の下2桁(00-99)
%m	月を表す数字(01-12)
%d	日(01-31)
## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題()
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

## 問題
```

```
### 解説

