# 処理の流れを変える
## if - 条件を満たすとき
### 後置if
  -  後置ifを使うとendがいらないため、プログラムを一行で書ける。
    [![Image from Gyazo](https://i.gyazo.com/c8c5833ead8afc2e5e7c7d7b116d6e7c.png)](https://gyazo.com/c8c5833ead8afc2e5e7c7d7b116d6e7c)
  - ifの条件が満たされないケース
    1.falseのとき
    2.nilのとき=>何もないことを表すオブジェクト
  - miketaさんより情報提供  
  > https://ysk-pro.hatenablog.com/entry/if-return-value

### unlessと！  
[![Image from Gyazo](https://i.gyazo.com/08dddb84a58de21580675c0af0dfaa73.png)](https://gyazo.com/08dddb84a58de21580675c0af0dfaa73) 
- unlessの条件に書かれた変数xはfalseが代入されているので条件が満たされない。
- ifの条件に書かれた！xの結果はtrueになり、条件を満たす。

## ifの条件に書けるもの
[![Image from Gyazo](https://i.gyazo.com/0cdb8aac2e45e56aaaf688e644aaf199.png)](https://gyazo.com/0cdb8aac2e45e56aaaf688e644aaf199)
- 今回のプログラムで書かれた条件100、abcはfalseでもnilでもないので、条件が満たされたと判定される。trueでなくても条件は満たされることがある。

## 複数の道から1つを選んで分岐する
[![Image from Gyazo](https://i.gyazo.com/c3d76c9a3d10e864c3e3ad014feea9e1.png)](https://gyazo.com/c3d76c9a3d10e864c3e3ad014feea9e1)
- when節の条件を先頭のwhenから順に判定して、最初に満たした箇所の処理を行う。
- 上の場合、500以上の条件を満たしているので、下の300以上の処理は実施されない。

## なんども繰り返す
- 繰り返しの記述の仕方
```
3.times do
 puts "a"
end

3.times {
 puts "a"
end

3.times do puts "a" end
```
- 条件つき繰り返し　while
[![Image from Gyazo](https://i.gyazo.com/214eb9078fe9bcf8c7b2b780cbfd807c.png)](https://gyazo.com/214eb9078fe9bcf8c7b2b780cbfd807c)

## 演習問題
[![Image from Gyazo](https://i.gyazo.com/10305f11ace303c25cac677db168afc6.png)](https://gyazo.com/10305f11ace303c25cac677db168afc6)
[![Image from Gyazo](https://i.gyazo.com/93c8d3fafbd092f7f9bbd40ac70a2fa6.png)](https://gyazo.com/93c8d3fafbd092f7f9bbd40ac70a2fa6)
