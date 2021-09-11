# 部品をつくる　ークラス
## クラスメソッドの定義
- def self.メソッド名
```
class Cafe
  def self.welcome
    "いらっしゃいませ"
  end
end
```
- class << selfと書いてからメソッドを定義する。
```
class Cafe
  class << self
    def welcome
      "いらっしゃいませ"
    end
  end
end
```
selfを使うとクラス名が変わった時に書き換えなくていいメリットがある。
## 演習問題
```
#1
p ({:coffee => 300, :caffe_latte => 400}).class
#2
p Hash.new

#3
class CaffeLatte
end
caffe_latte = CaffeLatte.new
p caffe_latte.class
#4
class Item
    def name
        "チーズケーキ"
    end
end
item = Item.new
p item.name
#5
class Item
    def name=(text)
        @name = text
    end
    def name
        @name
    end
end
item = Item.new
item.name= "チーズケーキ"
p item.name
#6
class Item
    def initialize
        puts "商品を扱うオブジェクト"
    end
end
Item.new
#7
class Item
    def initialize(name)
        @name = name
    end
    def name
        @name
    end
end
Item1 = Item.new("マフィン")
Item2 = Item.new("スコーン")
p Item1.name
p Item2.name
#8
class Drink
    def self.todays_special
        "ホワイトモカ"
    end
end
p Drink.todays_special
#9
class Item
    def name
        @name
    end
    def name=(text)
        @name = text
    end
end

class Food < Item
end
food = Food.new
food.name = "チーズケーキ"
puts food.name
```
