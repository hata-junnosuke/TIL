# 配列や繰り返し処理を理解する
## 配列
### 配列とは
- 配列とは複数のデータをまとめて格納できるオブジェクト。
- 存在しない要素を指定してもエラーではなくnilが返る
```
irb(main):001:0> a = [1,2,3]
=> [1, 2, 3]
irb(main):002:0> a[100]
=> nil
irb(main):003:0> a[4] = 50 #添字を飛ばして指定することができる。間にはnilが入る
=> 50
irb(main):004:0> a
=> [1, 2, 3, nil, 50]
irb(main):006:0> a.delete_at(3)#delete_at(n)で削除できる。（[]でない点に注意）
=> nil
irb(main):007:0> a
=> [1, 2, 3, 50]
```
### ブロック
 - ブロックとはメソッドの引数として渡すことのできる処理のかたまり。
```
numbers.each do |n|#|n|をブロックパラメータというらしい。
  sum += n
end
```

- each以外の使い方例
```
irb(main):007:0> a
=> [1, 2, 3, 50]
irb(main):008:1* a.delete_if do |n|　# 奇数なら削除する
irb(main):009:1*   n.odd?
irb(main):010:0> end
=> [2, 50]
```
- do..endと{}
  - 改行を含む場合は長いブロックの場合はdo..end
  - 1行でコンパクトに書きたい時は{}
```
numbers = [1,2,3,4]
sum = 0

numbers.each { |n|
  sum += n
}
sum #=>10
```

### ブロックを使う配列のメソッド
- map/collect
- select/find_all/reject
```
irb(main):014:0> a
=> [2, 50, 33]
irb(main):015:0> even_a = a.select { |n| n.even? } #真の要素を引き抜く（rejectは逆）
=> [2, 50]

```
- sum
```
irb(main):001:0> numbers = [1,2,3,4]
=> [1, 2, 3, 4]
irb(main):002:0> numbers.sum(5) #初期値を設定できる（何もなければ0）
=> 15

```

### 範囲（Range）
```
irb(main):001:0> range = 1..5 #..ならば1以上5以下
=> 1..5
irb(main):002:0> range.include?(5)
=> true
irb(main):003:0> range = 1...5 #...ならば1以上5未満
=> 1...5
irb(main):004:0> range.include?(5)
=> false

```
### 繰り返し処理を行う
```
irb(main):001:0> numbers = []
=> []
irb(main):002:0> (1..10).step(2) { |n| numbers << n } #stepメソッドで間隔を指定できる（この場合2回ごとに追加する）
=> 1..10
irb(main):003:0> numbers
=> [1, 3, 5, 7, 9]
```

### プラクティス
```
#rgb.eb
def to_hex(r,g,b)
  [r,g,b].sum('#') do |n|
    n.to_s(16).rjust(2,'0')
  end
end

def to_ints(hex)
  r = hex[1..2]
  g = hex[3..4]
  b = hex[5..6]

  #ints =[]
  # [r,g,b].each do |s|
  #   ints << s.hex#16進数から10進数にするメソッド
  # end
  # ints
  # リファクタリングすると

  [r,g,b].map do |s|#mapはブロックの戻り値を配列要素にして新しい配列を返すメソッド
    s.hex
  end
  
  #さらにリファクタリングすると
  # def to_ints(hex) = hex.scan(/\w\w/).map(&:hex)
end
```

## 配列についてもっと詳しく
- 添字を使うと添字の位置と取得する長さを指定することができる。
```
irb(main):001:0> a = [1,2,3,4,5]
=> [1, 2, 3, 4, 5]
irb(main):002:0> a[1, 3]
=> [2, 3, 4]
```
- values_atメソッドを使うと取得したい要素の添字を複数指定できる
```
irb(main):003:0> a.values_at(0,2,4)
=> [1, 3, 5]
```
- lastメソッドを使うと最後の要素を取れる。引数を渡すと個数も示せる。(firstメソッドは逆)
```
irb(main):004:0> a.last
=> 5
irb(main):005:0> a.last(2)
=> [4, 5]
```
### 要素の変更方法

### 配列の連結
- concat（破壊的）
- +演算子（どうしてもという場合以外はこっちを使うべし）

### 配列の和集合、差集合、積集合
- |,-,&はいづれも非破壊的。
```
irb(main):001:0> a = [1,2,3]
=> [1, 2, 3]
irb(main):002:0> b=[3,4,5]
=> [3, 4, 5]
irb(main):003:0> a | b  #和集合（合体！）
=> [1, 2, 3, 4, 5]
irb(main):004:0> a - b　　#差集合
=> [1, 2]
irb(main):005:0> a & b　　#積集合（共通部分を引っこ抜く）
=> [3]
```
### 多重代入で残りの全要素を配列として受け取る、または無視する（＊）

### 1つの配列を複数の引数やwhen節の条件として展開する
```
irb(main):006:0> a.push(b) #配列でぶち込むなら
=> [1, 2, 3, [3, 4, 5]]
irb(main):007:0> a.push(*b)　#要素だけを入れるなら*が必要
=> [1, 2, 3, [3, 4, 5], 3, 4, 5]
```
- when節

### メソッドの可変長引数
```
irb(main):001:1* def greet(*names)
irb(main):002:1*   "#{names.join('と')}、こんにちは"
irb(main):003:0> end
=> :greet
irb(main):006:0> greet('田中さん','佐藤さん')
=> "田中さんと佐藤さん、こんにちは"
```

### *で配列同士を非破壊的に連結する
- *付きで別の配列に置くと展開されて別々の要素になる

### ==で等しい配列かどうか判断する

### ％記法で文字列の配列を簡潔に作る

### 文字列を配列に変換する
- charsメソッド
` 'Ruby'.chars=> ["R", "u", "b", "y"]`
- splitメソッド

### 配列にデフォルト値を設定する
```
irb(main):001:0> a = Array.new #空の配列
=> []
irb(main):002:0> a = Array.new(5)　＃5つの要素が入る（デフォルトはnil）
=> [nil, nil, nil, nil, nil]
irb(main):003:0> a = Array.new(5, 0) #デフォルトを0で5つの要素を入れる
=> [0, 0, 0, 0, 0]
```
- 1,2,3,1,2,3と繰り返す配列
- デフォルト設定の注意

### ミューダブル、イミューダブル

## ブロックについてもっと詳しく
- with_indexメソッド
```
irb(main):001:0> fruits = ['apple', 'orange', 'melon']
=> ["apple", "orange", "melon"]
irb(main):002:0> fruits.each_with_index { |fruit, i| puts "#{i}: #{fruit}" }
0: apple
1: orange
2: melon
=> ["apple", "orange", "melon"]
```
- map,delete_ifメソッドでも使える
- 添字を0以外から始めることもできる

### 配列がブロックパラメータに渡される場合
```
irb(main):001:1* demensions = [
irb(main):002:1*   [10,20],
irb(main):003:1*   [30,40],
irb(main):004:1*   [50,60],
irb(main):005:0> ]
=> [[10, 20], [30, 40], [50, 60]]
irb(main):006:0> areas = []
=> []
irb(main):007:1* demensions.each do |length, width|
irb(main):008:1*   areas << length * width
irb(main):009:0> end
=> [[10, 20], [30, 40], [50, 60]]
irb(main):010:0> areas
=> [200, 1200, 3000]
```
### 番号指定パラメータ
？

### ブロックローカル変数

### 繰り返し処理以外でも使用されるブロック

　### do..endと{}の結合度の違い
 
 ## 範囲（Range）についてもっと詳しく
 - endless range
 ```
 irb(main):001:0> numbers = [10,20,30,40,50]
=> [10, 20, 30, 40, 50]
irb(main):002:0> numbers[1..]#2番目以降取ってくる
=> [20, 30, 40, 50]
irb(main):003:0> numbers[..1]＃2番目まで取ってくる
=> [10, 20]
 ```
 
 ## さまざまな繰り返し処理
 - timesメソッド
 - upto,downtoメソッド
```
irb(main):001:0> a = []
=> []
irb(main):002:0> 10.upto(14) { |n| a <<  n }
=> 10
irb(main):003:0> a
=> [10, 11, 12, 13, 14]
irb(main):004:0> a = []
=> []
irb(main):005:0> 14.downto(10) { |n| a << n }
=> 14
irb(main):006:0> a
=> [14, 13, 12, 11, 10]
```
 - stepメソッド
```
irb(main):008:0> 1.step(10,2) { |n| a << n }
=> 1
irb(main):009:0> a
=> [1, 3, 5, 7, 9]
```
 - while文とuntill文
  - whileは条件が真になるまで
  - untillは条件が偽である間繰り返す 
 - for文
  -　eachとの違いはローカル変数がfor文の外でも使えること 
 - loopメソッド
 - 再帰呼び出し
 
 ## 繰り返し処理用の制御構造
 - break
  - breakとreturnの違い
   - breakは繰り返し処理から出る 
   - returnはメソッドから出てしまう
 - throwとcatch
 - next（条件に合うと繰り返し処理を中断して次へ行かせる。）
 ```
 irb(main):001:0> numbers = [1,2,3,4,5]
=> [1, 2, 3, 4, 5]
irb(main):002:1* numbers.each do |n|
irb(main):003:1*   next if n.even?
irb(main):004:1*   puts n
irb(main):005:0> end
1
3
5
```
 - redo(繰り返しをやり直したいときに使う)
