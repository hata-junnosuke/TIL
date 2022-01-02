### プラクティス
```
def fizz_buzz(n)
  if n % 15 == 0
    'Fizz_Bizz'
  elsif n % 3 == 0
    'Fizz'
  elsif n % 5 == 0
    'Buzz'
  else
    n.to_s
  end
end
```


### 2.8.5　その他、文字列作成のいろいろ
```
# 配列の各要素を連結して1つの文字列にする
[10,20,30].join #=> "102030"

# *演算子を使って文字列を繰り返す(文字列に使えるとは知らなかった)
"HI!" * 10 #=> "Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!Hi!"
```

### 2.8.6 文字と文字列の違いはない
Rubyでは何文字でも「文字列（stringオブジェクト）」として扱われる

## 2.9 数値についてもっと詳しく
### 2.9.1 基数指示子を用いた整数リテラル

- 先頭に基数指示子を入れると10進数以外で表示できる

```
irb(main):001:0> 0b1111111 # 2進数（0b）
=> 127
irb(main):002:0> 011111　# 8進数（0または0o）
=> 4681
irb(main):003:0> 0xff # 16進数（0x）
=> 255

```

### 指数表現
```
「2×（10マイナス３乗）」
irb(main):004:0> 2e-3
=> 0.002

```

## 2.10 真偽値と条件分岐についてもっと詳しく
- &&や||が評価を終了するタイミング
```
1 && 2 && 3 #=> 3
1 && nil && 3 #=> nil
1 && false && 3 #=> false

nil || false #=> false
false || nil #=> nil
nil || false || 2 || 3 #=>2
```
なぜこうなるかはP58を確認

### 優先順位が低いand,or,not

```
高い　!
    &&
    ||
    not
低い and or
```

### unless
- elsifにあたるものはない。

### == true, == falseは冗長なので書かない
```
if s.empty?
  "から文字です"
end

if !n.zero?
  "ゼロではありません"
end  
```

### case
- elsifを重ねるよりcase文の方がシンプルになる。 
```
case 対象のオブジェクト
when 値1
　値1の時の処理
when 値2
　値2の時の処理
else 
 どれでもない時の処理
end
```

### 条件演算子
式　？　真だった時の処理　：　偽だった時の処理
```
n = 11
n > 10 ? "10より大きい"　: "10以下"
#=> "10より大きい"
```

## メソッド定義についてもっと詳しく
### デフォルト付きの引数
- Rubyではメソッドを呼び出す際に過不足があるとエラーになる
- デフォルトをつけることができる
```
def method(a = 1, b = 2)
  @method
end
```

### ?で終わるメソッド
- 真偽値を返すメソッド

### !で終わるメソッド
 - ！で終わるメソッドは危険という意味を持つ。（破壊的なものもあるが全てが破壊的ではないことも認識せよ）

### エンドレスメソッド定義（3.0から追加なのでベータ版のように様子見推奨）
- endを省略して1行でメソッドを定義できる
```
def greet = "hello"
greet #=>"hello"
```

## その他の基礎知識
### ガベージコレクション（GC）
- 
### エイリアスメソッド
- 全く同じ実装で名前だけ異なるメソッドのこと。EX sizeとlength

### 式と文
- 
### 擬似変数
- true, false, nilなど
- 代入はできない

### 組み込みライブラリ、標準ライブラリ、gem
- 
### requireとrequire_relative
```
# requireがないと標準ライブラリ（date）が使えない
- ライブラリの読み込み方
irb(main):001:0> Date.today
(irb):1:in `<main>': uninitialized constant Date (NameError)
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/lib/ruby/gems/3.0.0/gems/irb-1.3.5/exe/irb:11:in `<top (required)>'
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/bin/irb:23:in `load'
        from /Users/hatajunnosuke/.rbenv/versions/3.0.3/bin/irb:23:in `<main>'
irb(main):002:0> require 'date'
=> true
irb(main):003:0> Date.today
=> #<Date: 2022-01-02 ((2459582j,0s,0n),+0s,2299161j)>
```
- 自作のRubyプログラムを読み込む場合はrelative_requireを使う

### puts, print, p, pp
- puts, printは一般ユーザー向け
- p, ppは開発者向け
- putsは改行あり、printは改行なし
今のところこの認識でOK



