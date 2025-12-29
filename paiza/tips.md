# Paiza 学習帳
## やりたいこと

標準入力で与えられる **H×W 個の数値**を **2次元配列（grid）**として受け取りたい。

## 解決法

```ruby
h, w = STDIN.gets.split.map(&:to_i)
grid = Array.new(h) { STDIN.gets.split.map(&:to_i) }

p grid
```

## 入力例

```text
3 4
1 2 3 4
5 6 7 8
9 10 11 12
```

## 出力例

```ruby
[[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]]
```

## 補足

* `Array.new(h) { ... }` で **h 行分だけ** `gets` を回して 2次元配列にします
* `w` は検証用（行の要素数チェック等）に使えますが、受け取るだけなら未使用でもOKです
* 以下と同じ内容
```ruby
  grid = []

  h.times do
    grid << STDIN.gets.split.map(&:to_i)
  end
```

## 参考

* [https://docs.ruby-lang.org/ja/latest/class/Array.html](https://docs.ruby-lang.org/ja/latest/class/Array.html)
* [https://docs.ruby-lang.org/ja/latest/method/IO/i/gets.html](https://docs.ruby-lang.org/ja/latest/method/IO/i/gets.html)
* [https://docs.ruby-lang.org/ja/latest/method/String/i/split.html](https://docs.ruby-lang.org/ja/latest/method/String/i/split.html)
---
