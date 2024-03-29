#  RubyExamination
## 問題（stepメソッド）
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
## 問題(sort <=>)
```
arr = [5, 3, 8, 1, 4, 2, 6, 9, 0, 7]
arr.sort!{ |a, b | b <=> a }.reverse!
p arr
```
### 解説
Arrayクラスのsort!メソッドの問題です。

sort sort!
要素の並び替えは<=>演算子によって行われます。
(要素1 <=> 要素2)の結果が-1なら要素1が順番として先に並び、0ならそのまま、1なら要素2が先に並びます。
sort!メソッドはレシーバ自身を変更する破壊的メソッドです。
sortメソッドにブロックを渡すと戻り値によって要素を並び替えます。
ブロックの戻り値が-1ならaが先に並び、0ならそのまま、1ならbが先に並びます。
問題のプログラムではブロック内の変数aとbの位置を入れ替えています。
これにより<=>演算子の比較結果が逆になり、並びが降順になります。
ruby p [5, 3, 8, 1, 4, 2, 6, 9, 0, 7].sort!{ |a, b | a <=> b } #=> [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] p [5, 3, 8, 1, 4, 2, 6, 9, 0, 7].sort!{ |a, b | b <=> a } #=> [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
reverse reverse!
reverseメソッドは配列の要素の順番を逆に並び替えた配列を返します。
reverse!メソッドはレシーバ自身を変更する破壊的メソッドです。
問題のプログラムでは、sort!メソッドで降順に並び替え、さらにreverse!メソッドで要素の内容を反転させ変数arrの内容を変更しています。

よって問題のプログラムの出力は[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]となります。
## 問題(ハッシュ)
次のプログラムを実行して同じ結果が得られるプログラムを選んでください。
```
klass = Class.new

hash = {klass => 100}

puts hash[klass]
```
### 解説
ハッシュはキーにシンボルと文字列以外を指定して値を格納することもできます。
問題では、Classクラスのオブジェクトをキーに100を格納しています。
```
klass = Class.new

hash = {klass => 100}

puts hash #=> {#<Class:0x007f9a3f0a7068>=>100}
```
また、選択肢のうちキーを:klassとする、または{klass: 100}と記述するとキーがシンボルになります。
変数klassに格納されたClassクラスのオブジェクトをキーに指定していることにはなりません。
```
irb(main):026:0> klass = Class.new
=> #<Class:0x0000000104e0e5e8>
irb(main):027:0>
irb(main):028:0> hash = {}
=> {}
irb(main):029:0> hash.store(klass, 100)
=> 100
irb(main):030:0>
irb(main):031:0> puts hash[klass]
100
=> nil
irb(main):045:0> puts hash
{#<Class:0x0000000104e4d4a0>=>100}
=> nil
```
## 問題(partition、　a,の意味)
```
a, = (1..5).partition(&:odd?)
p a
```
### 解説
Enumerable#partitionはブロックの条件を満たす要素と満たさない要素に分割します。

問題では、a, =と指定されているため、Enumerable#partitionの条件を満たす要素がaに入ります。
条件を満たさない要素は変数が指定されていないため、無視されます。
```
#条件を満たすものの配列が左辺に配置され、"a,"はその左辺を表している
irb(main):049:0> a = (1..5).partition(&:odd?)
irb(main):050:0> p a
[[1, 3, 5], [2, 4]]
=> [[1, 3, 5], [2, 4]]
```
## 問題(変数)
```
省略
```
### 解説
変数には以下の特徴があります。

グローバル変数は$から始まります。
インスタンス変数は@から始まります。
クラス変数は@@から始まります。
定数は、アルファベット大文字で始まります。

## 問題(each_cons)
```
arr = (1..30).to_a
container = []

arr.each_cons(7) do |i|
  container << i
end

p container.length
```
### 解説
Array#each_cons(cnt)はselfからcnt個ずつ要素を取り出しブロックに渡します。ブロック引数には配列で渡されます。
取り出す要素は、[要素1, 要素2, 要素3], [要素2, 要素3, 要素4] ...と1つづ前に進みます。
引数の分配列を返して、先頭が次に変わっていくメソッド。

似たメソッドにArray#each_slick(cnt)があります。
以下が、それぞれの実行結果です。
```
(1..10).each_cons(3) {|arr| p arr }

# <実行結果>
# [1, 2, 3]
# [2, 3, 4]
# [3, 4, 5]
# [4, 5, 6]
# [5, 6, 7]
# [6, 7, 8]
# [7, 8, 9]
# [8, 9, 10]
```
## 問題(例外)
```
exceptions = {
  NameError => "変数または定数が存在しません",
  ArgumentError => "引数が不正です",
  RuntimeError => "特定の例外が発生しました"
}

begin
  raise
rescue => e
  puts exceptions[e.class]
else
  puts "例外は発生しませんでした"
end
```
### 解説
raiseは例外を発生させます。

第1引数に発生させる例外クラスを指定でき、第2引数にはメッセージを指定できます。第1引数には例外クラスのインスタンスを指定することもできます。

**引数は省略可能で例外クラスの指定を省略した場合はRuntimeErrorが発生します。**

rescue節は、例外クラスを指定しない場合StandardErrorとそのサブクラスが補足対象となり、補足する例外を指定することで指定した例外とそのサブクラスのみを補足します。

また=>を使用し任意の識別子(問題の場合e)を指定し、例外オブジェクトを参照することができます。

例外オブジェクトに対してmessageメソッドを使いその例外オブジェクトのエラーメッセージを取得できます。

例外処理におけるelse節はrescue節が実行されなかった場合に実行されます。
```
irb(main):158:0>
irb(main):159:1* exceptions = {
irb(main):160:1*   NameError => "変数または定数が存在しません",
irb(main):161:1*   ArgumentError => "引数が不正です",
irb(main):162:1*   RuntimeError => "特定の例外が発生しました"
irb(main):163:0> }
=> {NameError=>"変数または定数が存在しません", ArgumentError=>"引数が不正です", RuntimeError=>"特定の例外が発生しました"}
irb(main):164:0>
irb(main):165:1* begin
irb(main):166:1*   raise
irb(main):167:1* rescue => e
irb(main):168:1*   puts exceptions[e.class]
irb(main):169:1* else
irb(main):170:1*   puts "例外は発生しませんでした"
irb(main):171:0> end
特定の例外が発生しました
=> nil
```
## 問題(例外)
```
次のプログラムを実行するとどうなりますか。
raise ['Error Message']
```
### 解説
StandardErrorを継承しないクラスのインスタンスをraiseメソッドの引数に指定すると、TypeErrorが発生し次のメッセージが表示されます。
```
exception class/object expected
```
この問題ではraiseメソッドに配列を指定しているので、TypeErrorが発生し同様のメッセージが表示されます。
## 問題(IOクラス)
次の結果
```
# list.txt
1
2
3
4
```

```
io = File.open('list.txt')

while not io.eof?
  io.readlines
  io.seek(0, IO::SEEK_CUR)
  p io.readlines
end
```
### 解説
以下が、問題コードで使用されているメソッドと説明です。

IO#eof?: ファイルポインタが終端にある場合、trueを返します。
IO.readlines: ファイルから全てを読み込みます。
IO#seek(offset, whence): ファイルポインタをwhenceからoffsetまで移動します。
IO::SEEK_CUR: 現在のファイルポインタから
その他whenceで使用できる値はRubyリファレンスを参照してください。
4行目で、ファイルから全て読み込んだ時点で、ファイルポインタはファイル終端にあります。
5行目は、ファイル終端から0文字移動するため、6行目では[]のみ表示されます。

以下のコードは問題コードに行番号をつけています。
## 問題(deleteメソッド)
```
str = "-1234567890-"

XXXX

puts str

#結果 -1234-
```
### 解説
String#deleteメソッドは、引数に指定した文字を削除します。

メソッド名に!が付いていると破壊的メソッドになります。

引数の先頭の文字が^の場合は指定した文字以外の文字を削除します。

-は両端に文字がある場合は範囲指定となります。

,区切りで引数を複数指定した場合は、全ての引数にマッチした文字が削除対象となります。
```
s = "1234567890"

# 非破壊的
s.delete("^1-5")          # => "12345"
s.delete("1-5")           # => "67890"
s.delete("^1-5", "1-5")   # => "1234567890"
p s                       # => "1234567890"

# 破壊的
s.delete!("1-5")
p s                       # => "67890"
```
## 問題(ヒアドキュメント-)
```

```
### 解説
<<識別子行から識別子行の直前までを文字列として扱います。このような表現方法をヒアドキュメントといいます。
問題では、ヒアドキュメントにインデントを加えています。
<<識別子とした際は、インデントを加えることはできず、その場合は、エラーになります。

ヒアドキュメントにインデントを加える場合は、<<-識別子とします。
インデントを取り除くためには、Ruby 2.3から追加された<<~識別子を用います。
この問題ではインデントありで表示されることに注意
```
s = <<-EOF
    Hello,
    Ruby
    EOF
p s
# <実行結果>
#     Hello,\n    Ruby\n
```
## 問題(product,transpose)
```
arr = [1,2].product([3,4]).transpose
p arr
```
### 解説
productはレシーバーの配列と引数の配列からそれぞれ1つ要素を取り出し新しい配列を作成し、全ての配列を要素とする配列を返します。

productの例
```
[1, 2].product([3, 4])

# 実行結果
# [[1, 3], [1, 4], [2, 3], [2, 4]]
```
transposeはレシーバーの配列から行と列を入れ替えた配列を作成し返します。

transposeの例
```
[[1, 3],
 [1, 4],
 [2, 3],
 [2, 4]
].transpose

# 実行結果
# [[1, 1, 2, 2], [3, 4, 3, 4]]
```
## 問題(メソッド内での定数)
```
def hoge
  x = 10
  Y = x < 10 ? "C" : "D"
  puts Y
end
hoge
```
### 解説
Rubyではメソッド内で定数を定義することができません。
複数回メソッドを呼び出した場合に、定数が不定となるため定義できません。
宣言された場合は、SyntaxErrorが発生します。
## 問題(raiseの優先)
```
v1 = 1 - 1 == 0
v2 = v1 || raise RuntimeError
puts v2 && false

```
### 解説
行目を処理した後のv1はtrueになります。

2行目では、||の優先順位が高いため、v1 || raiseが評価されます。
しかし、後ろにRuntimeErrorがあるため、シンタックスエラーになっています。
この挙動を回避するには、以下の3つの方法があります。
```
v2 = v1 or raise RuntimeError
```
```
v2 = v1 || raise(RuntimeError)
```
```
v2 = v1 || (raise RuntimeError)
```
## 問題(equalとobject_id)
```
arr = [
  true.equal?(true),
  nil.eql?(NilClass),
  String.new.equal?(String.new),
  1.equal?(1)
]

p arr.collect { |a| a ? 1 : 2 }.inject(:+)
```
### 解説
ここではnilとNilClassを比較しているのでfalseになる
## 問題(flattenメソッド)
```
irb(main):043:0> ["apple", "banana", "orange"].flatten!
=> nil
irb(main):044:0> [["apple"],["banana"],["orange"]].flatten
=> ["apple", "banana", "orange"]
```
### 解説
Array#flattenはselfを再帰的に平坦化します(3次元配列を1次元配列の配列を返す)。
Array#flatten!は破壊的にselfを再帰的に平坦化します。平坦化が行われない場合は、nilを返します。

String#reverseはselfを逆順にした配列を返します。

%||は%記法といいます。文字列リテラル等を定義することができます。
詳しい説明は%記法を参照してください。
## 問題(メタ文字)
```
省略
```
### 解説
正規表現のメタ文字においてa-zとはaとzではなく、アルファベットのaからz全ての英小文字にマッチします。

メタ文字	対応する文字
[a-z]	abcdefghijklmnopqrstuvwxyz
[A-Z]	ABCDEFGHIJKLMNOPQRSTUVWXYZ
[0-9]	0123456789
[あ-ん]	あいえおかき...わをん
[]で囲むと[]内のいずれかの1文字にマッチするという表現になります。
.

改行以外の文字にマッチします。mオプションがあると改行にもマッチします。
```
p /./ =~ "\nRuby"
#=> 1
# 改行を無視しRにマッチします。初めにマッチした文字のindexを返します。
p /./ =~ "\n\n1234"
#=> 2
# 改行を無視し1にマッチします。初めにマッチした文字のindexを返します。
p /./m =~ "\nRuby"
#=> 0
# 正規表現オプションmを付けると複数業モードになり改行にもマッチします。
```
¥w, ¥W
英数字とアンダースコアにマッチします。

¥Wは¥w以外の文字にマッチします。
```
p /\w/ =~ "\t\s\n\r\f_RubyExamination"
#=> 5
# アンダースコアにマッチし、最初にマッチした文字のindexを返します。
p /\W/ =~ "RubyExamination_1234567890"
#=> nil
# 英数字アンダースコアどれにもマッチしなかったのでnilを返します。
```
¥d,¥D

数字にマッチします。

¥Dは¥d以外の文字にマッチします。
```
p /\d/ =~ "\t\s\n\r\f_Ruby2.1"
#=> 10
# 数字の2にマッチし、最初にマッチした文字のindexを返します。
p /\W/ =~ "1234567890"
#=> nil
# 数字以外の文字が存在しないのでマッチせず、nilを返します。
```
\s 空白文字にマッチする。(スペース、タブ、改行、復帰、垂直タブ)
\S 空白文字ではない文字とマッチする。(\s以外の1文字)
\A 文字列の先頭にマッチする。^とは違い改行の直後の文字ではない文字にマッチする。
\z 文字列の末尾にマッチする。(改行含む)
\Z 文字列の末尾にマッチする。(改行を含まない)
## 問題(和集合)
```
a1 = [1,2,3]
a2 = [4,2,3]

p a1 | a2
```
### 解説
Array#|は集合の和結合を行います。**重複する要素は取り除かれます**
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

