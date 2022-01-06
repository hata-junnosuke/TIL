# 正規表現を理解する
## 正規表現って何？
- 筆者いわく、「パターンを指定して文字列を効率よく検索・置換するためのミニ言語」
- 英語でregular expression略してRegexp,Regexとかいうらしい。

### 正規表現の便利さを知る
```
irb(main):002:0" text = <<TEXT
irb(main):003:0" I love Ruby
irb(main):004:0" Python is a great langueage
irb(main):005:0" Java and JavaScript are defferent
irb(main):006:0> TEXT
=> "I love Ruby\nPython is a great langueage\nJava and JavaScript are defferent\n"
irb(main):007:0> text.scan(/[A-Z][A-Za-z]+/)
=> ["Ruby", "Python", "Java", "JavaScript"]

irb(main):012:0" text = <<TEXT
irb(main):013:0" 私の郵便番号は1234567です。
irb(main):014:0" 僕の住所は6770056　兵庫県西脇市板波町1234だよ。
irb(main):015:0> TEXT
=> "私の郵便番号は1234567です。\n僕の住所は6770056　兵庫県西脇市板波町1234だよ。\n"
irb(main):021:0> puts text.gsub(/(\d{3})(\d{4})/){"#{$1}-#{$2}"}
私の郵便番号は123-4567です。
僕の住所は677-0056　兵庫県西脇市板波町1234だよ。
=> nil
```
### 正規表現をゼロから学習するための参考資料
https://qiita.com/jnchito/items/893c887fbf19e17d3ff9
- \d は「1個の半角数字（0123456789）」を意味するメタ文字です（文字の集合を表しているので、特に 「文字クラス」 と呼ばれます）。
- {n,m} は「直前の文字が n 個以上、m 個以下」の意味です。また、 {n} とすれば「ちょうど n 文字」の意味になります。
- 「AまたはBのいずれか1文字」表す場合は [AB] と書きます（文字の集合を表すので、これも 文字クラス の一種です）。[ ]の中の文字数に制限はありません。
- ただし、- だけは特別な意味を持つので注意が必要です。
  - 実は [a-z] と書くと、「aまたはbまたはcまたは・・・yまたはz」の意味になるのです。「aまたはハイフンまたはz」ではありません！
  - 同様に、[a-zA-Z0-9] であれば「aまたはbまたは・・・z、AまたはBまたは・・・Z、0または1または・・・9」の意味になります。（端的に言うと、これは「半角英数字1文字」の意味です）
- 正規表現は現実的には「100%完璧ではなく、そこそこ正しいレベルの正規表現」で妥協することがよくあります。 



## Rubyにおける正規表現オブジェクト
### Rubular
https://rubular.com/
[![Image from Gyazo](https://i.gyazo.com/bbee2bd4880582e6afda1c9e70d9e876.png)](https://gyazo.com/bbee2bd4880582e6afda1c9e70d9e876)
[![Image from Gyazo](https://i.gyazo.com/0d0d5680a70372599ce4d63b4fc768f4.png)](https://gyazo.com/0d0d5680a70372599ce4d63b4fc768f4)
- matchメソッド
```
irb(main):001:0> text = '私の誕生日は1977年7月17日です・'
=> "私の誕生日は1977年7月17日です・"
irb(main):002:0> m = /(\d+)年(\d+)月(\d+)日/.match(text)
=> #<MatchData "1977年7月17日" 1:"1977" 2:"7" 3:"17">
irb(main):003:0> m[0] #配列の番号とは違うので注意（1番目が0でない）
=> "1977年7月17日"
irb(main):004:0> m[1]
=> "1977"
```

### キャプチャに名前をつける
- `?<name>`
```
irb(main):004:0> m = /(?<year>\d+)年(?<month>\d+)月(?<day>\d+)日/.match(text)
=> #<MatchData "1977年7月17日" year:"1977" month:"7" day:"17">
irb(main):005:0> 
irb(main):006:0> m[:year]
=> "1977"
```
  
- 左辺に正規表現リテラル、右辺に文字列を置いて=~演算子を使うと、キャプチャの名前がそのままローカル変数に割り当てられる
```
irb(main):007:1* if /(?<year>\d+)年(?<month>\d+)月(?<day>\d+)日/ =~ text
irb(main):008:1*   puts "#{year}/#{month}/#{day}"
irb(main):009:0> end
1977/7/17
```

### 組み込み変数
- $ではじまる特殊な変数

### 正規表現と組み合わせると便利なStringクラスのメソッド
- scan
```
irb(main):001:0> '123 456 789'.scan(/\d+/)
=> ["123", "456", "789"]
```
- [],slice,slice!
```
irb(main):001:0> text = '郵便番号は123-4567です'
=> "郵便番号は123-4567です"

# 第2引数で取得も可能
irb(main):002:0> text[/\d{3}-\d{4}/]
=> "123-4567"
irb(main):003:0> text = '私の誕生日は1977年7月17日です・'
=> "私の誕生日は1977年7月17日です・"
irb(main):004:0> text[/(?<year>\d+)年(?<month>\d+)月(?<day>\d+)日/, 'day']
=> "17"
```
- split
- gsub,gsub!
```
irb(main):005:0> text = '123,456-789'
=> "123,456-789"
irb(main):006:0> text.split(',')
=> ["123", "456-789"]
irb(main):007:0> text.gsub(',', ':')
=> "123:456-789"
irb(main):008:0> text.gsub(/,|-/,':')
=> "123:456:789"
```
