# 小さく分割するーメソッド
## 演習問題
```
#1
def order
    puts "カフェラテをください"
end
order
#2
def area
    puts 3 * 3
end
area
#3
def dice
    arr = [1,2,3,4,5,6]
    p arr.sample
end
dice
#4
def order(item)
    puts "#{item}をください。"
end
order("カフェラテ")
order("モカ")
#5
def dice
    arr = [1,2,3,4,5,6]
    if arr.sample == 1
        puts "もう一回"
        puts arr.sample
    else
        puts arr.sample
    end
end
dice
#6
def price(item)
    if item == "コーヒー"
        puts 300
    elsif item == "カフェラテ"
        puts 400      
    end
end
price("コーヒー")
#7
def price(item:, size:)
    items = {"コーヒー"=>300, "カフェラテ"=> 400 }
    sizes = {"s"=>0, "t"=>50,"g"=>100 }
    items[item] + sizes[size]
end
puts price(item: "コーヒー",size: "t")
#8
def price(item:, size:"s")
    items = {"コーヒー"=>300, "カフェラテ"=> 400 }
    sizes = {"s"=>0, "t"=>50,"g"=>100 }
    items[item] + sizes[size]
end
puts price(item: "コーヒー")
#9
def order(drink = "コーヒー")
    puts "#{drink}をください。"
end
order
```
