# テストを自動化する
## テストコードのひな形
```
require 'minitest/autorun'
  
class SampleTest < Minitest::Test #クラス名はキャメルケース
  def test_sample                 #test_で始める
    asssert_equal 'RUBY', 'ruby'.upcase
  end
end
```

```
# aがb等しければパス
assert_equal b, a
# aが真であればパス
assert a
# aが偽であればパス
refute a
```

## テスト自動化
- 別のディレクトリでtestを作り、test_fizz_buzz.rbを作る
- 実行には`ruby test/test_fizz_buzz.rb`
```
# test_fizz_buzz.rb
require 'minitest/autorun'
require_relative '../lib/fizz_buzz'#ここを追加することで関連づいてテストできるようになる

class FizzBuzzTest < Minitest::Test
  def test_fizzbuzz
    assert_equal '1', fizz_buzz(1)
    assert_equal '2', fizz_buzz(2)
    assert_equal 'Fizz', fizz_buzz(3)
    assert_equal '4', fizz_buzz(4)
    assert_equal 'Buzz', fizz_buzz(5)
    assert_equal 'Fizz', fizz_buzz(6)
    assert_equal 'Fizz Buzz', fizz_buzz(15)
  end
end

$ ruby test/fizz_buzz_test.rb
Run options: --seed 42331

# Running:

.

Finished in 0.000408s, 2450.9813 runs/s, 17156.8692 assertions/s.

1 runs, 7 assertions, 0 failures, 0 errors, 0 skips

```
