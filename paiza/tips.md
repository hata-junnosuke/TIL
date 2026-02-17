# Paiza 学習帳
## 標準入力で与えられる **H×W 個の数値**を **2次元配列（grid）**として受け取りたい。

### 解決法

```ruby
h, w = STDIN.gets.split.map(&:to_i)
grid = Array.new(h) { STDIN.gets.split.map(&:to_i) }

p grid
```

### 入力例

```text
3 4
1 2 3 4
5 6 7 8
9 10 11 12
```

### 出力例

```ruby
[[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]]
```

### 補足

* `Array.new(h) { ... }` で **h 行分だけ** `gets` を回して 2次元配列にします
* `w` は検証用（行の要素数チェック等）に使えますが、受け取るだけなら未使用でもOKです
* 以下と同じ内容
```ruby
  grid = []

  h.times do
    grid << STDIN.gets.split.map(&:to_i)
  end
```

### 参考

* [https://docs.ruby-lang.org/ja/latest/class/Array.html](https://docs.ruby-lang.org/ja/latest/class/Array.html)
* [https://docs.ruby-lang.org/ja/latest/method/IO/i/gets.html](https://docs.ruby-lang.org/ja/latest/method/IO/i/gets.html)
* [https://docs.ruby-lang.org/ja/latest/method/String/i/split.html](https://docs.ruby-lang.org/ja/latest/method/String/i/split.html)
---
## 割り算で誤差や整数除算を起こさずに、正確な「逆数（1/x）」を扱いたい。

### 解決法
```ruby
# 整数 x の逆数を正確に扱う
x = 100
inv = Rational(1, x)

inv
````

### 入力例

```text
100
```

### 出力例

```ruby
(1/100)
```

### 補足

* `1 / x` は整数同士だと 0 になるため不適切
* `Rational` を使うと分数として正確に計算できる

### 参考

* [https://docs.ruby-lang.org/ja/latest/class/Rational.html](https://docs.ruby-lang.org/ja/latest/class/Rational.html)

---

  ## スライディングウィンドウ

  ### 概要

  配列の **連続するk個の要素の合計** を効率的に求める手法。
  ウィンドウを1つずらすたびに、 **左端を引いて右端を足す** だけで次の合計が得られる。

  ### 計算量の比較

  | 手法 | 計算量 | n=300,000, k=300,000 |
  |------|--------|----------------------|
  | 毎回k個を合計 | O(n*k) | 最大900億回 |
  | スライディングウィンドウ | O(n) | 最大30万回 |

  ### イメージ

  ```
  arr = [1, 2, 3, 2, 1]  k=3

  i=0: [1, 2, 3] 2, 1   sum = 1+2+3 = 6（初回は普通に合計）
  i=1:  1,[2, 3, 2] 1   sum = 6 - arr[0] + arr[3] = 6 - 1 + 2 = 7
  i=2:  1, 2,[3, 2, 1]  sum = 7 - arr[1] + arr[4] = 7 - 2 + 1 = 6
  ```

  更新式
  ```
  新しい合計 = 前の合計 - arr[i - 1] + arr[i + k - 1]
                          ^^^^^^^^^^   ^^^^^^^^^^^^^^
                          左端（出る）   右端（入る）
  ```
  ループ範囲
  - 有効な開始位置: 0 から n-k まで（全 n-k+1 個）
  - 最初の1つはループ前に計算するので、ループは (1..n-k)

  基本テンプレート
  ```
  n, k = gets.split.map(&:to_i)
  arr = gets.split.map(&:to_i)

  # 最初のウィンドウの合計
  score = arr[0...k].sum
  max_score = score

  # 1つずつずらして更新
  (1..n - k).each do |i|
    score = score - arr[i - 1] + arr[i + k - 1]
    max_score = score if score > max_score
  end

  p max_score
  ```
  応用例: 最大合計の個数と最初の位置
  ```
  n, k = gets.split.map(&:to_i)
  arr = gets.split.map(&:to_i)

  score = arr[0...k].sum
  max_score = score
  count = 1
  earliest = 1

  (1..n - k).each do |i|
    score = score - arr[i - 1] + arr[i + k - 1]
    if score > max_score
      max_score = score
      count = 1
      earliest = i + 1
    elsif score == max_score
      count += 1
    end
  end

  puts "#{count} #{earliest}"
  ```
  参考

  - https://docs.ruby-lang.org/ja/latest/method/Array/i/sum.html
  - https://docs.ruby-lang.org/ja/latest/class/Range.html
