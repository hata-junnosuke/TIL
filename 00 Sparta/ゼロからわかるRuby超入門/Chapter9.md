# 部品を共同利用する　ーモジュール
## 複数のクラスでメソッドを共同利用する
1. モジュールを作る
```
module WhippedCream
end
```

2. モジュールにメソッドを定義する
```
module WhippedCream
    def whipped_cream
        @name += "ホイップクリーム"
    end
end
```

3. モジュールのメソッドをクラスで使う 
```
module WhippedCream
    def whipped_cream
        @name += "ホイップクリーム"
    end
end

class Drink
    include WhippedCream
    def initialize(name)
        @name = name
    end
    def name
        @name
    end
end

drink = Drink.new("モカ")
drink.whipped_cream
puts drink.name
```
- クラスでincludeメソッドを使うと引数で指定したモジュールのメソッドを、クラス自身のインスタンスメソッドのように使えるようになる。
- モジュールの優れたところは、複数のクラスで使えること。

## モジュールのメソッドや定数をそのまま使う
### モジュールはインクルードしてメソッドを提供する使い方のほかに、クラスメソッドや定数を定義して呼び出せる。
- クラスメソッド
```
module WhippedCream
    def self.info
        "トッピング用ホイップクリーム"
    end       
end

puts WhippedCream.info #=>トッピング用ホイップクリーム
```
- 定数(定数は大文字で始めることと：：でつなぐ点に注意)
```
module WhippedCream
   Price = 100       
end

puts WhippedCream::Price #=>100
```
### Rubyが用意しているモジュールもある。
```
puts Math::PI #=>3.141592...
```
### 名前空間
- 同じクラス名を複数の場所で使いたいが、別のクラスなので別々に定義して呼び分けたいとき。
```
module BecoCafe
    class Coffee
        def self.info
            "深みと香りのコーヒー"
        end
    end
end

module MachuCafe
    class Coffee
        def self.info
            "豊かな甘味のコーヒー"
        end
    end
end
puts BecoCafe::Coffee.info #=>深みと香りのコーヒー
puts MachuCafe::Coffee.info #=>豊かな甘味のコーヒー
```
## 部品を別ファイルに分ける
- require_relatveメソッドを使うと、別ファイルに定義されたクラスやモジュールを読み込んで使うことができる。

## 演習問題
```
#1
module  ChocolateChip
    def chocolate_chip
        @name += "チョコレートチップ"
    end
end

#2
class Drink
    include ChocolateChip
    def initialize(name)
        @name = name
    end
    def name
        @name
    end
end

mocha = Drink.new("モカ")
mocha.chocolate_chip
puts mocha.name

#3
module EspressoShot
    Price = 100
end
puts EspressoShot::Price

#4
require_relative "hi"
include WhippedCream
puts WhippedCream.info

#hi.rb
#module WhippedCream
#def self.info
#    "トッピング用ホイップクリーム"
#end
#end
```
