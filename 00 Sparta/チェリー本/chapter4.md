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

- do..endと{}
  - 改行を含む場合は長いブロックの場合はdo..end
  - 1行でコンパクトに書きたい時は{}
```
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

4.7から
