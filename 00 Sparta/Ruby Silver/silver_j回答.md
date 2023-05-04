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
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  ## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  
## 問題
### 
  

