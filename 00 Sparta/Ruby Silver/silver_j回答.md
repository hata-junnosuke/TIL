# 解説
## 問題1
### 予約語
![スクリーン ショット 2023-05-04 に 11 22 15 午前](https://user-images.githubusercontent.com/81806676/236097262-bb776c07-2077-49d6-b739-a9b70eb1d049.png)

予約語はRuby上で他の用途が決められているもの？
予約語はクラス名、変数名などに用いることはできません。
ただし接頭辞$, @、@@が先頭についたものは予約語とは見なされません。
また、def のあとやメソッド呼び出しのピリオドのあとなど、メソッド名であるとはっきり分かる場所ではメソッド名として用いることができます。
> https://qiita.com/yait/items/a9d9383cbf56ff2ac17b
## 問題2

### 可変長引数(rest引数)
*がついた引数は可変長引数（rest引数）です。fooメソッドでは最低１つの引数を必要とします。第一引数は変数aで得ることができ、残りの引数は配列に格納されます。
  
## 問題4
### Timeクラス
+ otherでother秒後の日時が返されます。TimeクラスはTime#to_s、Time#to_iなどの変換やmktimeで任意の日時のオブジェクトを作成する方法を理解しておく必要があります。
1日は24（h）*60(m)*60(s)で掛け算して示すことができる。2日はこれを*2してやればいい。
> https://www.sejuku.net/blog/16204  
## 問題5
### ローカル変数
１文字目はアルファベット小文字か_で始める必要があります。また、２文字目以降はアルファベットもしくは数字を使用します。予約語はローカル変数として使用できません。
  
## 問題7
### downtoメソッド
 Integer#downto(min)は、引数minまで数を１ずつ減らしながら実行されます。その後、selectメソッドで、要素に対してブロックの評価が真であった要素をすべて含む配列が返されます。
## 問題8
### Enumerable
 各要素に対してブロックを評価した結果を全て含む配列を返します。Enumerableは頻出問題です。Enumerable#collectに対するEnumerable#map、findに対するdetectなど同じ挙動をするメソッドはRubyの公式ドキュメントを見て覚えておきましょう。
> 公式 https://docs.ruby-lang.org/ja/latest/class/Enumerable.html
## 問題9
### deleteメソッド
String#deleteはself（メソッドのレシーバーとなったオブジェクト）に含まれる文字を取り除いた文字列を生成して返します。
```
irb(main):006:0> puts "Ruby on Rails".delete("Rails")
uby on
=> nil
```
## 問題10
### ヒアドキュメント
ヒアドキュメントは <<識別子' を含む行の次の行から 識別子' だけの行の直前までを文字列とする行指向のリテラルです。（Ruby公式ドキュメントより抜粋） 終端行の識別子前に余分な空白を含めるとエラーとなります。ただし、<<-識別子' のように -' を付けて書くことで終端行をインデントすることは可能です。

```
irb(main):001:0" doc = <<EOF
irb(main):002:0"  The quick brown fox
irb(main):003:0"  jumps over the lazy dog
irb(main):004:0> EOF
=> " The quick brown fox\n jumps over the lazy dog\n"
irb(main):005:0> p doc
" The quick brown fox\n jumps over the lazy dog\n"
=> " The quick brown fox\n jumps over the lazy dog\n"
```
## 問題14
### chopメソッド
String#chopは文字列末尾の文字を1文字削除するためのメソッドです。String#chompが改行文字を対象するのに対しchopはすべての文字を対象とします。ただし、文字列末尾が\r\nの場合は、2文字とも削除します。String#chop!はレシーバーの文字列自体を変更する破壊的メソッドです。
```
\n           改行                newline        (0x0A)
\r           復帰                return         (0x0D)
```
> 改行っぽいもの https://qiita.com/akicho8/items/efe59578f12d6b7f5626
## 問題17
### concatメソッド（引数の配列をそのまま連結）→sortは順番に並び替える
```
irb(main):007:0> odd = [1,3,5]
=> [1, 3, 5]
irb(main):008:0> even = [2,4,6]
=> [2, 4, 6]
irb(main):009:0> num = odd + even
irb(main):010:0> p num.sort
[1, 2, 3, 4, 5, 6]
=> [1, 2, 3, 4, 5, 6]
irb(main):013:0> p num.concat
[1, 3, 5, 2, 4, 6]
=> [1, 3, 5, 2, 4, 6]
```
  
## 問題18
### 正規表現
`/^[hc].*o$/i`

Silverの試験ではアルファベットの大文字小文字を含んだ単語の区別、文字列と数字の簡単な組み合わせなど簡単な正規表現について理解していることを要求されます。問題の正規表現はhもしくはcで始まりoで終わる文字列とマッチします。またiのオプションを付けることにより大文字、小文字の区別を無視しています。
## 問題20
###  File#join
File#joinは定数FILE::SEPARATOR "/"を使って文字列を連結します。  
## 問題22
### sliceメソッド
String#slice(nth, len)は文字列のnth目からlen文字の文字列を作って返します。
String#sliceは非破壊的なので、文字列"test code"が表示されます。代りに破壊的なメソッドであるString#slice!を使用すると、"code"と表示されます。
```
irb(main):014:0> string = "test code"
=> "test code"
irb(main):015:0> string.slice(0,4)
irb(main):016:0> p string
"test code"
=> "test code"
irb(main):017:0> string.slice!(0,4)
=> "test"
irb(main):018:0> p string
" code"
=> " code"
```
## 問題23
### 
Ruby 2.0以降では、デフォルトのスクリプトエンコーディングはUTF-8です。

## 問題24
### keyを使ったメソッド
 以下のメソッドはHashがkeyをキーとして持つときはtrueを返します。
Hash#has_key?(key) -> bool
Hash#include?(key) -> bool
Hash#key?(key) -> bool
Hash#member?(key) -> bool
## 問題26
### マッチ
問題のコードでは正規表現パターンにマッチした文字列が繰り返しブロック引数に渡され実行されます。matchが一度しか正規表現によるマッチを行わないのに対し、scanは繰り返しマッチを行います。正規表現の\wは単語構成文字 [a-zA-Z0-9_]の省略記法です。今回は単語をハッシュのキー、出現回数を値として、単語の出現回数に応じて値をインクリメントさせています。
```
irb(main):021:0> s = "To be or not to be, that is the question."
irb(main):022:0> hash = Hash.new(0)
=> {}
irb(main):023:0> s.scan(/\w+/){|i| hash[i] += 1}
=> "To be or not to be, that is the question."
irb(main):024:0> hash["be"]
=> 2

irb(main):027:0> s = "To be or not to be, that is the question."
irb(main):028:0> hash = Hash.new(0)
=> {}
irb(main):029:0> s.match(/\w+/){|i| hash[i] += 1}
=> 1
irb(main):030:0> hash["be"]
=> 0
irb(main):031:0> p hash
{#<MatchData "To">=>1}
=> {#<MatchData "To">=>1}
```
  
## 問題27
### chrメソッド（エンコードして返す。できなかったらRangeErrorを返す。）
Integer#chrは文字コードに対応する1文字の文字列を返します。 p 12354.chr(Encoding::UTF_8) #=>"あ" selfを引数で与えられたエンコーディングで正しく解釈できない場合は、RangeErrorが発生します。

## 問題29
### 論理演算子
論理演算子の評価方法について、&&演算子においては左辺が真と評価されたときのみ右辺も評価されます。同じように||演算子においては、右辺が評価されるのは、左辺が偽と評価された場合です。
```
irb(main):032:0> ary = []
=> []
irb(main):033:0> ary << 1 && false
=> false
irb(main):034:0> true || ary << 2
=> true
irb(main):035:0> false && ary << 3
=> false
irb(main):036:0> false || ary << 4
irb(main):037:0> p ary
[1, 4]
=> [1, 4]
```
  
## 問題30
### 演算子の優先順位
String#**は定義されていません。 演算子の優先順位は*よりも**が高いため高い 2**2回(４回)"foo"が繰り返される新しい文字列を返します。
  
## 問題32
### IOクラス
IO#getsとIO#readlineはファイルオブジェクトから一行読み込んで、読み込みに成功した時にはその文字列を返します。IO#getsとIO#readlineの違いはEOFに到達した時の振る舞いのみです。IO#getsはnil, IO#readlineはEOFErrorを返します。
## 問題35
### 正規表現
\d、または[0-9]は数字を表しています。{m}は直前の正規表現のm回の繰り返しを意味します。
  
## 問題37
### File.openのモード
モードを明示的に指定しない場合、読み込みモード"r"でファイルを開きます。そのため、エラーが発生しています。選択肢Bの場合は、IOErrorではなくErrno::ENOENT（システムコールに依存したエラー）を発生させます。例題では、読み込みモードを"w"として開いた場合のファイルの内容は、Time.nowで得た現在時刻を指定したformat文字列で出力したものになります。(例：2015/06/07)　 以下、ファイルオープン時のモード一覧を示します。
"r" 読み込みモード
"w" 書き込みモード。ファイルが存在していればファイルを空にする。
"a" 書き込みモード。ファイルが存在していれば、ファイルの末尾から追記する。
"r+" 読み書き両用モード。ファイルの先頭から読み書きを行う。
"w+" 読み書き両用モード。ファイルが存在していればファイルを空にする。
"a+" 読み書き両用モード。ファイルの末尾から読み書きを行う。
## 問題39
### deleteメソッド
String#deleteメソッドでは引数で指定された文字をselfから取り除きます。
-の両端に文字列がある場合は範囲指定をしていることになります。
例題では"0-5"で0から5までの数字を取り除きますが、続く"8-"では範囲指定とは見なされず、8と-を削除します。
  
## 問題41
### eqlとequal
メソッド名の最後に?がつくメソッドは通常tureもしくはfalseを返します。String#eql?メソッドは変数aと変数bの文字列が同じ場合にtrueを返します。一方でString#equal?メソッドは変数aと変数bがひとつの同じ文字列を「参照」している場合はtrueとなります。実際にobject_idを確認してみると、値が違うことがわかります。


## 問題42
### injectメソッド
inject (Enumerable)は前回のブロックの戻り値をブロックに渡して繰り返し実行します。 例題では条件演算子を使い、２つの整数値を比較しより大きな値を取り出しそれをブロックに渡しています。

条件演算子(文法） 式1 ? 式1が真だった場合の値 : 式1が偽だった場合の値
  
## 問題46
### クラス変数
@numと@@numはそれぞれ、インスタンス変数とクラス変数です。クラス変数はクラス定義の中で定義され、クラスの特異メソッド、インスタンスメソッドなどから参照／ 代入ができます。例題ではFooクラスとBarクラスの間で値を共有します。@はインスタンス変数です。インスタンス変数は名前の通り特定のオブジェクトに所属しています。(クラスはclassクラスのオブジェクトのためクラスレベルでのインスタンス変数も存在する）Rubyのオブジェクトモデルをより深く理解したい場合は、Silver合格後にGold試験の受験もおすすめします。


  
## 問題48
### detectメソッド
Enumerable#detectメソッドは要素に対してブロックを評価した値が真になった最初の要素を返します。例題では1~20までの数字に対して一番最初の5の倍数を求めていますので正解はCです。Enumerable#findを使っても同様の結果になります。
  
# 補足
## IOクラス
> Ruby IOクラスについて学ぶ https://qiita.com/nk-ty/items/bd0a5295e540e27936b3

  

