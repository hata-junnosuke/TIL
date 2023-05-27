# RubyExam
## 問題(インスタンス変数)
```
class C
  def v=(other) # ここから
    @v = other
  end
  def v
    @v
  end           # ここまで
end

c = C.new
c.v = 100
p c.v
```
### 解説
attr_readerはインスタンス変数を返すメソッド(def v\ end)を作成します。
attr_writerはインスタンス変数を変更するメソッド(def v=\ end)を作成します。
attr_accessorはインスタンス変数を返すメソッドと変更するメソッドを作成します。
## 問題(Objectクラス)
```
class Object
  CONST = "1"
  def const_succ
    CONST.succ!
  end
end

class Child1
  const_succ
  class << self
    const_succ
  end
end

class Child2
  const_succ
  def initialize
    const_succ
  end
end

Child1.new
Child2.new

p Object::CONST
```
### 解説
クラスObjectにメソッドを定義すると特異クラスでもそのメソッドを利用することが出来ます。
問題のプログラムを順に実行すると、答えは"5"になります。

補足　Object#const_succについて
内部でString#succ!を実行しています。このメソッドはレシーバーの文字列を次の文字列へ進めます。
この問題ですと、"1"→"2"・・・と1ずつ繰り上がります。
また、定数に対して行っていますが破壊的メソッドの呼び出しですので再代入にはならず警告は表示されません。


## 問題(クラス変数と特異クラス)
```
class C
  @@val = 10
end

module B
  @@val = 30
end

module M
  include B
  @@val = 20

  class << C
    p @@val
  end
end
```
### 解説
この問題ではクラスCの特異クラス定義をモジュールMで行っています。
クラス変数はレキシカルに決定されますので答えは20です。
## 問題(例外)
```
begin
  raise
rescue => e
  puts e.class
end
```
### 解説
引数なしでraiseを呼び出すと、RuntimeError例外が発生します。
## 問題(Dateクラス同士の減算)
```

```
### 解説
Dateクラス同士の減算はRationalになります。

その他、似たクラスの演算を以下にまとめます。

演算	戻り値クラス
Date同士の減算	Rational
Time同士の減算	Float
DateTime同士の減算	Rational
## 問題(モジュールのnesting)
```
module SuperMod
end

module SuperMod::BaseMod
  p Module.nesting
end
```
### 解説
Module.nestingはネストの状態を表示します。

次のプログラムを実行すると、[SuperMod]と表示されます。
```
module SuperMod
  p Module.nesting
end
```
モジュールがネストされた場合は、ネストの状態をすべて表示します。
SuperMod::BaseModのようにプログラムを書くとモジュールSuperModの内側にモジュールBaseModがあることを表現することが出来ます。
インデントして別々に書いた場合に比べて、プレフィックスがある場合は内側にあるモジュールしかネストの状態は表示されません。
```
module SuperMod
  p Module.nesting # [SuperMod]
end

module SuperMod
  module BaseMod
    p Module.nesting # [SuperMod::BaseMod, SuperMod]
  end
end

module SuperMod::BaseMod
  p Module.nesting # [SuperMod::BaseMod]
end
```
この問題は[SuperMod::BaseMod]が表示されます。
## 問題(クラス変数)
```
class S
  @@val = 0
  def initialize
    @@val += 1
  end
end

class C < S
  class << C
    @@val += 1
  end

  def initialize
    @@val += 1
    super
  end
end

C.new
C.new
S.new
S.new

p C.class_variable_get(:@@val)
```
### 解説
以下のタイミングで@@valに加算されます。

クラスメソッドが定義された。
C.newが呼び出された。
superからCのinitializeを呼び出された。
S.newが呼び出された。

## 問題(フリップフロップ回路)
```
10.times{|d| print d < 2...d > 5 ? "O" : "X" }
```
### 解説
Integer#timesは0からself -1までの数値を順番にブロックに渡すメソッドです。

d < 2...d > 5の部分は条件式に範囲式を記述しています。
この式は、フリップフロップ回路のように一時的に真偽を保持するような挙動をとります。

詳細は、Rubyリファレンスに詳しい説明がありますのでそちらを参照してください。（https://docs.ruby-lang.org/ja/2.0.0/doc/spec=2foperator.html#range）

条件式として範囲式が用いられた場合には、状態を持つ sed や awk 由来の 特殊な条件式として振る舞います。

「..」の場合:

初期状態では式1だけを評価し、式1が真を返すまでは false を返します。

式1が真を返すと true を返します。式2が真なら初期状態に戻ります。

この後は式2だけを評価し、式2が真を返すまで true を返します。

式2が真を返すと true を返したあと、初期状態に戻ります。

「...」の場合:

初期状態では式1だけを評価し、式1が真を返すまでは false を返します。

式1が真を返すと true を返します。

この後は式2だけを評価し、式2が真を返すまで true を返します。

式2が真を返すと true を返したあと、初期状態に戻ります。
```
irb(main):078:0> 10.times{|d| print d < 2...d > 5 ? "O" : "X" }
OOOOOOOXXX=> 10
```
## 問題(instance_eval)
```
m = Module.new

CONST = "Constant in Toplevel"

_proc = Proc.new do
  CONST = "Constant in Proc"
end

m.instance_eval(<<-EOS)
  CONST = "Constant in Module instance"

  def const
    CONST
  end
EOS

m.module_eval(&_proc)

p m.const
```
### 解説
メソッドconstは特異クラスで定義されていますので、実行することができます。
その中で参照している定数CONSTはレキシカルに決定されますので、"Constant in Module instance"が表示されます。

instance_evalはブロックを渡す場合と、文字列を引数とする場合でネストの状態が異なります。
ブロックを渡した場合はネストは変わりませんが、文字列を引数とした場合は期待するネストの状態になります。ネストが変わらない状態で定数の代入を行うと、再代入になり警告が表示される場合があります。
例えば、次のプログラムではmodule_evalに文字列を引数とするとモジュールを再オープン、または定義したネストと同じです。
```
module M
  p Module.nesting # [M]
end

M.module_eval(<<-EVAL)
  p Module.nesting # [M]
EVAL

M.instance_eval do
  p Module.nesting # []
end

module M
  p Module.nesting # [M]
end
```
## 問題(prepend)
```
module M
  def foo
    super
    puts "M#foo"
  end
end

class C2
  def foo
    puts "C2#foo"
  end
end

class C < C2
  def foo
    super
    puts "C#foo"
  end
  prepend M
end

C.new.foo
```
### 解説
prependはモジュールのメソッドを特異メソッドとして追加します。
メソッド探索順はselfの後に追加されます。

複数モジュールを指定した場合は、左側が先にメソッド探索されます。
## 問題(例外の省略)
```
begin
  raise "Err!"
rescue => e
  puts e.class
end
```
### 解説
raiseの例外クラスを省略した場合は、RuntimeErrorを発生させます。
rescueの例外クラスを省略した場合は、StandardErrorを捕捉します。
RuntimeErrorはStandardErrorのサブクラスです。


## 問題(Enumerator::Lazy)
```
p (1..10).lazy.map{|num|
  num * 2
}.take(3).inject(0, &:+)
```
### 解説
lazyはEnumerator::Lazyクラスを返します。
Enumerator::Lazyクラスはmapやselectメソッドに遅延評価を提供します。

take(3)が実行されると1から3までmapに渡されたものと判断され、injectに渡されます。
よって、答えは12になります。
この時、4から10までの評価は発生しません。

## 問題(Kernelと特異クラス)
```
class C
end

p C.singleton_class.singleton_class.singleton_class.singleton_class
```
### 解説
singleton_classはKernelモジュールにあるインスタンスメソッドです。
```
m = C.method :singleton_class
p m.owner # Kernel
```
特異クラスの継承関係にKernelモジュールがあるため、singleton_classを呼び出すことが出来るため、特異クラスの特異クラスを取得するようなことができます。
```
class C
end

p C.singleton_class # #<Class:C>
p C.singleton_class.singleton_class # #<Class:#<Class:C>>
p C.singleton_class.singleton_class.singleton_class # #<Class:#<Class:#<Class:C>>>
p C.singleton_class.singleton_class.singleton_class.singleton_class # #<Class:#<Class:#<Class:#<Class:C>>>>
```
この問題ではsingleton_classを4回呼び出していますので#<Class:#<Class:#<Class:#<Class:C>>>>が答えになります。


## 問題(Hashの中身を渡す)
```
def foo(arg1:100, arg2:200)
  puts arg1
  puts arg2
end

option = {arg2: 900}

foo arg1: 200, **option
```
### 解説
キーワード引数へHashオブジェクトを渡すことができます。
Hashの中身を渡す必要があるので、変数の前に**を付加します。
## 問題(定数とfreeze)
```
CONST_LIST_A = ['001', '002', '003']
begin
  CONST_LIST_A.map{|id| id << 'hoge'}
rescue
end

CONST_LIST_B = ['001', '002', '003'].freeze
begin
  CONST_LIST_B.map{|id| id << 'hoge'}
rescue
end

CONST_LIST_C = ['001', '002', '003'].freeze
begin
  CONST_LIST_C.map!{|id| id << 'hoge'}
rescue
end

CONST_LIST_D = ['001', '002', '003'].freeze
begin
  CONST_LIST_D.push('add')
rescue
end

p CONST_LIST_A
p CONST_LIST_B
p CONST_LIST_C
p CONST_LIST_D
```
### 解説
変数は1文字目を大文字にすると定数になります。定数には次の特徴があります。

代入を行うと警告が発生しますが、値は変更されます。
中身を直接変更した場合は値が変わります。ただし、警告は発生しません。
特徴1の例
```
CONST = ["001", "002", "003"]
CONST = ["A", "B", "C"]
p CONST

# <実行結果>
# warning: already initialized constant CONST
# warning: previous definition of CONST was here
# ["A", "B", "C"]
```
特徴2の例
```
CONST = ["001", "002", "003"]
CONST[0] = "A"
p CONST

# <実行結果>
# ["A", "002", "003"]
```
freezeはオブジェクトを凍結します。凍結されたオブジェクトは次の特徴があります。

破壊的な操作ができません。
オブジェクトの代入ができます。
自作クラスのインスタンス変数をfreezeしない限り、変更できます。
特徴1の実行結果
```
hoge = "hoge".freeze
hoge.upcase!
p hoge

# <実行結果>
# can't modify frozen String (RuntimeError)
```
特徴2の実行結果
```
hoge = "hoge".freeze
hoge = "foo".freeze
puts hoge

# <実行結果>
# foo
```
特徴3の実行結果
```
class Fish
  attr_accessor :name
  def initialize(name)
    @name = name
  end
end

liberty = Fish.new("liberty")
liberty.name.upcase!
puts liberty.name

# <実行結果>
# LIBERTY
```
```
irb(main):103:0> p CONST_LIST_A
["001hoge", "002hoge", "003hoge"]
=> ["001hoge", "002hoge", "003hoge"]
irb(main):104:0> p CONST_LIST_B
["001hoge", "002hoge", "003hoge"]
=> ["001hoge", "002hoge", "003hoge"]
irb(main):107:0>  p CONST_LIST_C
["001", "002", "003"]
=> ["001", "002", "003"]
irb(main):106:0> p CONST_LIST_D
["001", "002", "003"]
["001", "002", "003"]
=> ["001", "002", "003"]
```
## 問題(モジュールのinclude)
```
module M
  def foo
    super
    puts "M#foo"
  end
end

class C2
  def foo
    puts "C2#foo"
  end
end

class C < C2
  def foo
    super
    puts "C#foo"
  end
  include M
end

C.new.foo
```
### 解説
includeはモジュールのメソッドをインスタンスメソッドとして追加します。
メソッド探索順はselfの後に追加されます。

```
irb(main):177:0> C.new.foo
C2#foo
M#foo
C#foo
=> nil
```
## 問題(ネストの状態)
```
次のプログラムは"Hello, world"と表示します。
同じ結果になる選択肢はどれですか（複数選択）

module M
  CONST = "Hello, world"

  class C
    def awesome_method
      CONST
    end
  end
end

p M::C.new.awesome_method

```
### 解説
定数の参照はレキシカルに行われます。
M::C#awesome_methodのコンテキストにCONSTがないため例外が発生します。
```
module M
  CONST = "Hello, world"
end

class M::C
  def awesome_method
    CONST
  end
end

p M::C.new.awesome_method
```
class_evalにブロックを渡した場合は、ブロック内のネストはモジュールMになります。
そのコンテキストから定数を探しますので"Hello, world"が表示されます。
```
class C
end

module M
  CONST = "Hello, world"

  C.class_eval do
    def awesome_method
      CONST
    end
  end
end

p C.new.awesome_method
```
class_evalに文字列を渡した場合のネストの状態はクラスCです。
CONSTはクラスCにありますので"Hello, world"が表示されます。
```
class C
  CONST = "Hello, world"
end

module M
  C.class_eval(<<-CODE)
    def awesome_method
      CONST
    end
  CODE
end

p C.new.awesome_method
```
class_evalにブロックを渡した場合は、ブロック内のネストはモジュールMになります。
そのコンテキストから定数を探しますがないため例外が発生します。
```
class C
  CONST = "Hello, world"
end

module M
  C.class_eval do
    def awesome_method
      CONST
    end
  end
end

p C.new.awesome_method
```
## 問題(requireとload)

次の2つのプログラムを実行するとどうなりますか

lib.rbの内容
```
module Lib
  $num += 1
end
```
program.rbの内容
```
$num = 0
1..10.times do |n|
  require './lib.rb'
end
puts $num
```

### 解説
requireはRubyライブラリをロードします。

requireとloadの違い

requireは同じファイルは1度のみロードする、loadは無条件にロードする。
requireは.rbや.soを自動補完する、loadは補完は行わない。
requireはライブラリのロード、loadは設定ファイルの読み込みに用いる。

## 問題(Refinement)
```
class C
  def m1
    400
  end
end

module M
  refine C do
    def m1
      100
    end
  end
end

class C
  using M
end

puts C.new.m1
```
### 解説
Refinementは有効化したスコープのみに影響を与えることが出来ます。
この問題ではクラスオープンした際にusingでRefinementを有効化していますが、
スコープ外は無効になります。

よって、puts C.new.m1とした結果は400になります。
## 問題(initializeはPrivate)
```
class C
public
  def initialize
  end
end

p C.new.private_methods.include? :initialize
```
### 解説
initializeはpublicなどでアクセス修飾子をつけたとしても、privateから変わることはありません。
## 問題(Module#refine)
```
class C
  def self.m1
    200
  end
end

module R
  refine C do
    def self.m1
      100
    end
  end
end

using R

puts C.m1
```
### 解説
Module#refineは無名のモジュールを作成します。ブロック内のselfは無名モジュールになります。
```
class C
end

module M
  refine C do
    self # 無名モジュールを指します
  end
end
```
Refinementでクラスメソッドを再定義する場合は次のようにsingleton_classを使います。ブロックの中でself.m1としないのがポイントです。
```
class C
  def self.m1
    'C.m1'
  end
end

module M
  refine C.singleton_class do
    def m1
      'C.m1 in M'
    end
  end
end

using M

puts C.m1 # C.m1 in M と表示されます。
```
この問題では、クラスメソッドは再定義していませんので200が表示されます。
## 問題(prepend)
```
module M1
end

module M2
end

class C
  prepend M1, M2
end

p C.ancestors
```
### 解説
 prependはモジュールのメソッドを特異メソッドとして追加します。
メソッド探索順はselfの前に追加されます。

複数モジュールを指定した場合は、左側が先にメソッド探索されます。
## 問題(Singletonモジュール)
```
require 'singleton'

class Message
  include Singleton

  def morning
    'Hi, good morning!'
  end
end

p Message.__(1)__.morning
```
### 解説
Singletonモジュールをインクルードすると、クラスメソッドinstanceを定義します。

このメソッドを呼び出すことでクラスのインスタンスを１つだけ返します。
```
require 'singleton'

class Message
  include Singleton

  def morning
    'Hi, good morning!'
  end
end

p Message.instance.__id__ # 70163722811160
p Message.instance.__id__ # 70163722811160
```
オブジェクトIDがすべて同じ値を返していることから、インスタンスが１つだけ返していることがわかる。
選択肢にあるinstantize、object、singletonはSingletonモジュールをインクルードしても定義されません。


 ## 問題(定数の探索)
```
class Human
  NAME = "Unknown"

  def name
    NAME
  end
end

class Noguchi < Human
  NAME = "Hideyo"
end

puts Noguchi.new.name
```
### 解説
定数はインスタンスではなくクラスに存在します。
定数の探索順位はクラス内 -> スーパークラス -> クラス探索順に行われます。

よって、Human#nameのクラス内定数であるNAME = "Unknown"が返されます。
## 問題(alias)
```

```
### 解説
alias式はメソッドやグローバル変数に別名を付けることができます。
定義は以下のようにします。
```
alias new_method old_method
alias :new_method :old_method
alias $new_global_val $old_global_val
```
メソッド内でメソッドに別名をつける必要がある場合は、Module#alias_methodを使います。
```
alias_method "new_method", "old_method"
alias_method :new_method, :old_method
```
## 問題(YAML)
```
require 'yaml'

yaml = <<YAML
  sum: 510,
  orders:
    - 260
    - 250
YAML

object = YAML.__(1)__

p object
```
### 解説
文字列のYAMLデータをHashオブジェクトにしています。

YAML.loadは引数を１つ取り、YAMLデータをRubyオブジェクトにします。
引数は文字列でも良いですし、IOオブジェクトでも良いです。

read、new、openはYAMLモジュールにないメソッドです。


## 問題(lambda)
```
local = 0

p1 = lambda { |arg1, arg2|
  arg1, arg2 = arg1.to_i, arg2.to_i
  local += [arg1, arg2].max
}

p1.call("1", "2")
p1.call("7", "5")
p1.call("9")

p local
```
### 解説
lambdaをcallする際の引数は省略できません。

lambdaに似た機能にProcがあります。
似ていますが、異なる部分もあります。
次の表がlambdaとProcの違いになります。

特徴	Proc	lambda
引数の数	曖昧	厳密
引数の渡し方	Proc.new { \	x, y\
return, brake, next	call以降が実行されない	call以降も実行される
問題の解答はエラーが発生する
## 問題(メソッドと変数の探索順位)
```
def foo(n)
  n ** n
end

foo = Proc.new { |n|
  n * 3
}

puts foo[2] * 2
```
### 解説
メソッドと変数の探索順位は変数が先です。

Procはcallまたは[]で呼び出すことができます。

問題では、foo[2]と宣言されているため、探索順の早いProcオブジェクトが呼び出されます。
もし、foo 2と宣言された場合は、メソッドが選択されます。
```
irb(main):220:1* def foo(n)
irb(main):221:1*   n ** n
irb(main):222:0> end
=> :foo
irb(main):223:0>
irb(main):224:1* foo = Proc.new { |n|
irb(main):225:1*   n * 3
irb(main):226:0> }
=> #<Proc:0x0000000110015b10 (irb):224>
irb(main):227:0>
irb(main):228:0> puts foo[2] * 2
12
=> nil
irb(main):229:0>
irb(main):230:0> puts foo 2 * 2 # 4 **4をしている 
256
=> nil
```

## 問題(loadは外部ライブラリ)
```
module M
  def foo
    super
    puts "M#foo"
  end
end

class C2
  def foo
    puts "C2#foo"
  end
end

class C < C2
  def foo
    super
    puts "C#foo"
  end
  load M
end

C.new.foo
```
### 解説
loadはrequire同様に外部ライブラリを読み込みます。

問題では、外部ライブラリではなく、モジュールを指定しているためエラーになります。


## 問題()
```
while not DATA.eof?
  lines = DATA.readlines
  lines.map(&:chomp!)
  lines.reverse
  p lines
end

__END__
1
2
3
4
```
### 解説
__END__はRubyスクリプトの終わりを表します。
__END__以降の文字列は組み込み定数DATAに入ります。
DATAはFileクラスのオブジェクトです。

1行目: `File#eof?`はファイルの終わりまで読み込んだかを返します。
2行目: `File#readlines`はファイルから全て読み込みます。内容は各行を要素にもつ配列になります。
3行目: `lines`の中身を`chomp!`で破壊的に末尾の改行を取り除きます。
4行目: `reverse`は配列の内容を逆順にします。このメソッドは破壊的メソッドではないため、`self`の内容は変わりません。

## 問題(JSON)
```
require 'json'

json = <<JSON
{
  "price":100,
  "order_code":200,
  "order_date":"2018/09/20",
  "tax":0.8
}
JSON

hash = __(1)__
p hash

```
### 解説
JSON形式の文字列をHashオブジェクトにするメソッドを選ぶ必要があります。

JSON.loadまたは、JSON.parseは引数にJSON形式の文字列を指定するとHashオブジェクトに変換します。

## 問題(extend)
```
module Parent
  def method_1
    __method__
  end
end

module Child
  # ここに挿入する選択肢を選んでください。
end

p Child::method_1
```
### 解説
モジュールをレシーバーにして、メソッドを呼び出すにはKernel#extendを利用します。

Kernel#extendは特異クラスへモジュールをインクルードします。モジュールParentにあるメソッドを呼び出す必要がありますので次の選択肢が答えのひとつになります。
```
module Parent
  def method_1
    __method__
  end
end

module Child
  extend Parent
end

p Child::method_1
```
また、Kernel#extendはモジュールの継承関係も含めてメソッド探索の対象になります。そのため、モジュールParentをインクルードしたあとでKernel#extendを呼び出すことでメソッドを呼び出すことができます。
```
module Parent
  def method_1
    __method__
  end
end

module Child
  include Parent
  extend self
end

p Child::method_1
```
ただし、Kernel#extendは呼び出した時点のモジュール継承関係がコピーされますので、その後でモジュールをインクルードして継承関係を更新してもメソッドを見つけることができません。
## 問題(Fixnumクラスのメソッド＋)
```
class Integer
  def +(other)
    self.-(other)
  end
end

p 1 + 1
```
### 解説
数値はFixnumクラスのオブジェクトです。FixnumクラスはIntegerクラスのサブクラスです。

問題では、Integerクラスへ+メソッドをオープンクラスし、オーバーロードているように取れます。
しかし、+メソッドはFixnumクラスで定義されています。
よって、元のFixnum#+が呼ばれます。


## 問題(ブロック変数)
```
1: def hoge(*args, &block)
2:   block.call(args)
3: end
4: 
5: hoge(1,2,3,4) do |*args|
6:   p args.length < 0 ? "hello" : args
7: end
```
### 解説
1行目で引数の値を配列として受け取り、ブロックに配列を渡しています。

5行目でブロック変数を渡していますが、*argsと宣言されているので、[[1, 2, 3, 4]]が渡されます。

6行目でargs.length < 0の結果は偽となり、[[1, 2, 3, 4]]が出力されます。
## 問題()
```
class C
  def m1
    200
  end
end

module R
  refine C do
    def m1
      100
    end
  end
end

using R

c = C.new
puts c.m1
```
### 解説
Ruby 2.1からRefinementが導入されました。
オープンクラスによる影響をローカルにとどめる為にRefinementがあります。

RefinementはModule#refineを呼び出すことで定義することが出来ます。
Module#usingで定義したRefinementを有効化することができます。

この問題では有効化した後ですので、メソッドを読んでもエラーにならずに100が表示されます。
## 問題(block)
```
def m1(*)
  str = yield if block_given?
  p "m1 #{str}"
end

def m2(*)
  str = yield if block_given?
  p "m2 #{str}"
end

m1 m2 do
  "hello"
end
```
### 解説
問題のコードで使用されているメソッド類は以下の通りです。

Kernel#block_given?はブロックが渡された場合は、真になります。
yieldはブロックの内容を評価します。
{ }はdo endよりも結合度が高い為、実行結果に差が出ます。
問題のコードは以下のように解釈されます。

m1の引数と解釈されるため、m2の戻り値はm2が表示されます。
m1へdo .. endのブロックが渡されます。よって、m1 helloが表示されます。
## 問題(ブロック引数)
```
def bar(&block, n)
  block.call
end

bar(5) {
  puts "hello, world"
}
```
### 解説
引数名に&を付与することでブロック引数になります。
ブロック引数は他の引数より後に記述します。
上のコードはエラーになる


## 問題(module_evalに文字列)
```
module A
  B = 42

  def f
    21
  end
end

A.module_eval(<<-CODE)
  def self.f
    p B
  end
CODE

B = 15

A.f

```
### 解説
module_evalに文字列を引数とした場合は、レシーバーのスコープで評価されます。
問題のプログラムを次のようにするとネストの状態を調べることができます。
```
A.module_eval(<<-CODE)
  p Module.nesting # [A]と表示され、モジュールAのスコープにあることがわかる
CODE
```
定数は静的に探索が行われますので、A::Bの42が答えになります。
## 問題(モジュールにクラスメソッドを定義)
```

```
### 解説
モジュールにクラスメソッドを定義するには３つ方法があります。
この問題の答えは次のとおりです。

```
module M
  extend self
  def a
    100
  end
end

p M.a

```

```
module M
  def a
    100
  end

  module_function :a
end

p M.a

```

```
module M
  class << self
    def a
      100
    end
  end
end

p M.a

```
## 問題(インスタンスメソッドの追加方法)
```
module M
  def method_a
    __method__
  end
end

class C
  include M
end

p C.new.method_a

# ここに当てはまるプログラムを選んでください。

p C.new.method_b

```
### 解説
インスタンスメソッドの追加の方法についての問題です。

問題ではmethod_bを追加する選択肢を選ぶ必要があります。
クラスC、モジュールMのスコープでメソッド名にselfがあるとクラスメソッドになります。

選択肢のひとつとしてまず、クラスCを再オープンしてメソッドを追加する方法が選べます。
```
class C
  def method_b
    __method__
  end
end
```
また、クラスCにモジュールMをインクルードしているので、　モジュールMにあるメソッドテーブルを探索できます。

そのため、モジュールMにメソッドを追加することで期待する結果を得ることができます。

次の選択肢も答えになります。
```
module M
  def method_b
    __method__
  end
end
```
## 問題(定数とネスト)
```
module K
  CONST = "Good, night"
  class P
  end
end

module K::P::M
  class C
    CONST = "Good, evening"
  end
end

module M
  class C
    CONST = "Hello, world"
  end
end

class K::P
  class M::C
    p CONST
  end
end
```
### 解説
クラスK::PにあるクラスM::Cはトップレベルにあるものとは異なります。
ネスト状態が同じものがあれば、そのレキシカルスコープから定数の探索を行います。
この問題では定数CONSTが参照しているのはK::P::M::Cで、そのレキシカルスコープにある定数を探索しますので"Good, evening"と表示されます。
```
module K
  class P
    p Module.nesting # [K::P, K]と表示されます
  end
end

module K::P::M
  class C
    p Module.nesting # [K::P::M::C, K::P::M]と表示されます
  end
end

module M
  class C
    p Module.nesting # [M::C, M]と表示されます
  end
end

class K::P
  class M::C
    p Module.nesting # [K::P::M::C, K::P]と表示されます
  end
end
```
## 問題(Rational)
```
val = 1 + 1/2r
puts val.class
```
### 解説
1/2rはRationalのインスタンスが作成されます。

FixnumとRationalの演算はRationalになります。

(Ruby 2.4からFixnumとBignumが統合されIntegerになりました)

その他のクラス演算を以下にまとめます。

演算	戻り値クラス

FixnumとRationalの演算	Rational

FloatとRationalの演算	Float

FixnumとComplexの演算	Complex

FloatとComplexの演算	Complex

Date同士の減算	Rational

Time同士の減算	Float

DateTime同士の減算	Rational


## 問題(定数の参照)
```
module M1
  class C1
    CONST = "001"
  end

  class C2 < C1
    CONST = "010"

    module M2
      CONST = "011"

      class Ca
        CONST = "100"
      end

      class Cb < Ca
        p CONST
      end
    end
  end
end
```
### 解説
使われている定数の場所がネストされている場合は内側から順に定数の探索が始まります。
レキシカルスコープに定数がない場合は、スーパークラスの探索を行います。

クラスCbから最も物理的に近いのはM2::CONSTであるため答えは"011"になります。
スーパークラスの探索はこの場合には行われません。
## 問題(キーワード引数)
```
def foo(arg:)
  puts arg
end

foo 100
```
### 解説
問題のコードはArgumentError: missing keyword: argが発生します。

argはキーワード引数と言います。キーワード引数は省略することができません。

問題のコードは次のように修正します。
```
def foo(arg:)
  puts arg
end

foo arg: 100
```

## 問題(using)
```
class C
end

module M
  refine C do
    def m1
      100
    end
  end
end

class C
  def m1
    400
  end

  def self.using_m
    using M
  end
end

C.using_m

puts C.new.m1
```
### 解説
usingはメソッドの中で呼び出すことは出来ません。呼び出した場合はRuntimeErrorが発生します。


## 問題(スコープ)
```
module M
  def refer_const
    CONST
  end
end

module E
  CONST = '010'
end

class D
  CONST = "001"
end

class C < D
  include E
  include M
  CONST = '100'
end

c = C.new
p c.refer_const
```
### 解説
refer_constはモジュールMにありますが、CONSTはレキシカルに決定されるためモジュールMのスコープを探索します。
この問題ではCONSTが見つからないため例外が発生します。
## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説

## 問題()
```

```
### 解説
