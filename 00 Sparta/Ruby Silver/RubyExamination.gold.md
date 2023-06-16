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

そのほか、lambdaはアロー演算子で定義することができます。
```
calc = -> (x, y) { x * y }
calc.call(5, 3) #=> 15
```
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
1: def hoge(*, &block)
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

## 問題(ブロックの結合度)
```
def m1(*)
  str = yield if block_given?
  p "m1 #{str}"
end

def m2(*)
  str = yield if block_given?
  p "m2 #{str}"
end

m1 m2 {
  "hello"
}
```
### 解説
問題のコードで使用されているメソッド類は以下の通りです。

Kernel#block_given?はブロックが渡された場合は、真になります。
yieldはブロックの内容を評価します。
{ }はdo endよりも結合度が高い為、実行結果に差が出ます。
問題のコードは以下のように解釈されます。

m2へブロックが渡され、m2 helloが表示されます。
m1へは引数が渡され、ブロックは渡されません。よって、m1が表示されます。
```
m1 (m2 {
      "hello"
    }
)

# 実行結果
# "m2 hello"
# "m1 "
```
問題のコードをdo endで置き換えた場合は以下の実行結果になります。
```
m1 m2 do  # m1(m2) do と解釈されます。
  "hello"
end

# 実行結果
# "m2 "
# "m1 hello"
```

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
## 問題(Kernel)
```
Kernelモジュールで定義されているメソッドを選んでください。
```
### 解説
Kernel#Array、Kernel#Hash、Kernel#StringはKernelのモジュール関数として定義されています。
Kernel#Dateはありません。

これらのメソッドは次のように使います。
```
p Array("Awesome Array") #=> ["Awesome Array"]
p Hash(awesome_key: :value) #=> {:awesome_key=>:value}
p String('0123456789') #=> "0123456789"
```

## 問題(lazy)
```
次のプログラムはEnumerator::Lazyを使っています。
先頭から5つの値を取り出すにはどのメソッドが必要ですか
(1..100).each.lazy.chunk(&:even?)
```
### 解説
値を取り出すには、Enumerator::Lazy#forceまたはEnumerator::Lazy#firstを呼び出す必要があります。

問題文には「先頭から5つ」とあるので、first(5)として取り出します。
また、Enumerator::Lazy#forceで問題文の通りにするにはEnumerator::Lazy#takeも利用します。

Enumerator::Lazy#takeはEnumerable#takeと違いEnumerator::Lazyのインスタンスを戻り値にします。
そのインスタンスからEnumerator::Lazy#forceで実際の値を取り出します。


```
irb(main):094:0> i = (1..100).each.lazy.chunk(&:even?)
=> #<Enumerator::Lazy: ...>
irb(main):095:0>
irb(main):096:0> i.take(5)
=> #<Enumerator::Lazy: ...>
irb(main):097:0> i.take(5).force
=> [[false, [1]], [true, [2]], [false, [3]], [true, [4]], [false, [5]]]
irb(main):098:0> i.first(5)
=> [[false, [1]], [true, [2]], [false, [3]], [true, [4]], [false, [5]]]
irb(main):099:0> i.first(5).force
(irb):99:in `<main>': undefined method `force' for [[false, [1]], [true, [2]], [false, [3]], [true, [4]], [false, [5]]]:Array (NoMethodError)
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/lib/ruby/gems/3.1.0/gems/irb-1.6.4/exe/irb:9:in `<top (required)>'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `load'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `<main>'
```
## 問題(継承のタイミング)
```
class Base
  def name
    p 'Base#name'
  end
end

module Scope
  class Base
    def name
      p 'Scope::Base#name'
    end
  end

  class Inherited < Base
    def name
      p 'Scope::Inherited#name'
      super
    end
  end
end

inherited = Scope::Inherited.new
inherited.name

```
### 解説
クラスInheritedの親クラスBaseがどのように決定されるかがこの問題のポイントです。
クラスはRubyでは定数です。定数の探索はレキシカルスコープを利用します。

親クラスBaseの探索はモジュールScopeから始まります。
レキシカルスコープにクラス（定数）Baseが見つかったので、クラスInheritedの親クラスBaseはScope::Baseとなります。
```
class Base
  def name
    p 'Base#name'
  end
end

module Scope
  class Base
    def name
      p 'Scope::Base#name'
    end
  end

  class Inherited < Base # クラスScope::Baseとして解釈される
    def name
      p 'Scope::Inherited#name'
      super
    end
  end
end
```
もし、クラスBaseがクラスInheritedより前に定義されていないのであれば動作が変わります。
継承を定義した時点でScope::BaseをRubyは見つけることができないので、親クラスBaseはトップレベルにあるクラスを参照します。
```
class Base
  def name
    p 'Base#name'
  end
end

module Scope
  class Inherited < Base # トップレベルにあるクラスBaseとして解釈される
    def name
      p 'Scope::Inherited#name'
      super
    end
  end

  class Base
    def name
      p 'Scope::Base#name'
    end
  end
end

inherited = Scope::Inherited.new
inherited.name

# 結果は次の通り
# "Scope::Inherited#name"
# "Base#name"
```
## 問題(method_missing)
```
module M
  def method_missing(id, *args)
    puts "M#method_missing"
  end
end
class A
  include M
  def method_missing(id, *args)
    puts "A#method_missing"
  end
end
class B < A
  def method_missing(id, *args)
    puts "B#method_missing"
  end
end

obj = B.new
obj.dummy_method
```
### 解説
method_missingは、継承チェーンを辿った末にメソッドが見つからなかった場合に、呼び出されます。
method_missingも継承チェーンを辿ります。

よって、B#method_missingが出力されます。


## 問題()
```
p Class.method_defined? :new
p String.method_defined? :new
p Class.singleton_class.method_defined? :new
p String.singleton_class.method_defined? :new
```
### 解説
Stringクラスはクラスメソッドnewでインスタンスを生成します。
StringクラスはClassクラスのインスタンスですので、StringクラスにあるnewはClassクラスのインスタンスメソッドです。
また、ClassクラスはRubyで多種多様なクラスを生成しますので、Classにもクラスメソッドとしてnewがあります。
```
str = String.new("Awesome String") # new は Class のインスタンスメソッド
# str.new こういった呼び方は出来ない
Klass = Class.new # new は Class のクラスメソッド
```
表示される内容の組み合わせは次のとおりです。
```
p Class.method_defined? :new #=> ture
p String.method_defined? :new #=> false
p Class.singleton_class.method_defined? :new #=> ture
p String.singleton_class.method_defined? :new #=> ture
```
## 問題(Enumeratorオブジェクトの作成)
```
module Enumerable
  def with_prefix(prefix)
    return to_enum(__(1)__, prefix) { size } unless block_given?

    each do |char|
      yield "#{prefix} #{char}"
    end
  end
end

[1,2,3,4,5].with_prefix("Awesome").reverse_each {|char|
  puts char
}
# 実行結果
Awesome 5
Awesome 4
Awesome 3
Awesome 2
Awesome 1
```
### 解説
ブロックを渡さない場合は、Enumeratorオブジェクトを作成してメソッドをチェーン出来るようにします。

Enumeratorオブジェクトを作成するためには、to_enumまたは、enum_forを呼びます。これらの引数にメソッド名をシンボルで指定することでチェーンした先でブロックを渡されたときにどのメソッドを評価すればよいかが分かります。

この問題では、with_prefixを再び評価する必要がありますので、__method__または:with_prefixを引数に指定します。__method__はメソッドの中で呼び出すと、そのメソッド名になります。
```
def awesome_method
  __method__
end

p awesome_method # :awesome_methodとシンボルでメソッド名が分かります
```
## 問題()
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
クラス変数はクラスに所属するあらゆるもので情報を共有する為にあり、
特異クラス定義の中でクラス変数を定義してもレキシカルに決定されます。

次のプログラムではクラス変数は共有されます。
```
class C
  class << self
    @@val = 10
  end
end

p C.class_variable_get(:@@val) # 10が表示される
```
この問題ではクラスCの特異クラス定義をモジュールMで行っています。
クラス変数はレキシカルに決定されますので答えは20です。


## 問題(Fiber)
```
次のプログラムで例外FiberErrorが発生する、Fiber#resumeの組み合わせを選択肢から選んでください。

f = Fiber.new do
  Fiber.yield 15
  5
end

```
### 解説
Fiberは軽量スレッドを提供します。

Fiber#resumeを実行するとFiber.yieldが最後に実行された行から再開するか、Fiber.newに指定したブロックの最初の評価を行います。

サンプルプログラムを実行して、処理の内容を見てみましょう。
```
f = Fiber.new do |name|
  Fiber.yield "Hi, #{name}"
end

p f.resume('Matz') # 'Hi, Matz'と表示されます。
p f.resume('Akira') # 'Akira'と表示されます。
p f.resume('Steve') # FiberErrorが発生します。
```
1. f.resume('Matz')を実行する。
2. Fiber.newのブロックを評価し、引数nameには'Matz'をセットする。
3. 変数を展開して、'Hi, Matz'をFiber.yieldの引数にセットする。
4. Fiber.yield('Hi, Matz')を実行すると、f.resume('Matz')の戻り値が'Hi, Matz'になる。Fiber.yield('Hi, Matz')は終了せず、次のf.resume('Akira')の実行を待つ。
5. f.resume('Akira')を実行するとFiber.yield('Hi, Matz')の戻り値が'Akira'になる。
6. ブロックの最終行なので、'Akira'がf.resume('Akira')の戻り値になる。
問題の処理の内容は次のとおりです。
```
f = Fiber.new do
  Fiber.yield 15
  5
end

f.resume
f.resume
f.resume
f.resume
```
1. １行目のf.resumeを実行する。
2. Fiber.newのブロックを評価する。
3. Fiber.yield(15)を実行すると、１行目のf.resumeの戻り値が15になる。Fiber.yield(15)は終了せず、次の２行目のf.resumeの実行を待つ。
4. ２行目のf.resumeを実行すると、Fiber.yield(15)の戻り値がnilになる。なお、Fiber#resumeに引数なしで実行すると、Fiber.yieldの戻り値はnilになります。
5. 処理を次の行に移り、ブロック最終行の5が２行目のf.resumeの戻り値になります。
6. ３行目のf.resumeを実行するが、ブロックの評価が終わっているので例外FiberErrorが発生します。
この問題の答えは、３回以上呼び出す
```
f.resume
f.resume
f.resume
```
または、
```
f.resume
f.resume
f.resume
f.resume
```
がこの問題の答えになります。
## 問題(定数の参照)
```
class C
  CONST = "Good, night"
end

module M
  CONST = "Good, evening"
end

module M
  class C
    CONST = "Hello, world"
  end
end

module M
  class C
    p CONST
  end
end
```
### 解説
この問題ではクラスCにある定数CONSTを参照していますが、トップレベルにあるものとは別になります。

例えば次のプログラムですと、クラスCが2つあるように見えます。
ネストを調べるとモジュールMにあるクラスCはM::Cと表示されています。
このように修飾された場合は同じ名前であっても別のものになります。
```
class C
  p Module.nesting # [C]と表示されます
end

module M
  class C
    p Module.nesting # [M::C, M]と表示されます
  end
end
```
この問題では修飾済みのM::CにあるCONSTを参照しますので、"Hello, world"を表示します。
## 問題(Refinementのsuper)
```
class C
end

module M
  refine C do
    def m1(value)
      super value - 100
    end
  end
end

class C
  def m1(value)
    value - 100
  end
end

using M

class K < C
  def m1(value)
    super value - 100
  end
end

puts K.new.m1 400
```
### 解説
superを実行した場合にもRefinementが影響します。理解しやすいようにそれぞれのメソッドにコメントと計算の途中結果を追加しました。
```
class C
end

module M
  refine C do
    def m1(value)
      p "define m1 using Refinement"
      super value - 100 # 300 - 100
    end
  end
end

class C
  def m1(value)
    p "define m1 in C"
    value - 100 # 200 - 100
  end
end

using M # ここからRefinementが有効になる

class K < C
  def m1(value)
    p "define m1 in K"
    super value - 100 # 400 - 100
    # Refinementが有効なのでsuperはモジュールMにあるm1を参照する
  end
end

puts K.new.m1 400
```
プログラムを実行するとコメントは次の順に表示されます。
```
"define m1 in K"
"define m1 using Refinement"
"define m1 in C"
```
superを実行したクラスの親クラスにRefinemnetがあれば同名のメソッドを探索して実行します。  

さらに、Refinementのなかでsuperを実行するとRefinementの対象クラスのメソッドを探索します。


## 問題(定数の参照)
```
class Ca
  CONST = "001"
end

class Cb
  CONST = "010"
end

class Cc
  CONST = "011"
end

class Cd
  CONST = "100"
end

module M1
  class C0 < Ca
    class C1 < Cc
      class C2 < Cd
        p CONST

        class C2 < Cb
        end
      end
    end
  end
end
選択

```
### 解説
Rubyは定数の参照はレキシカルに決定されますが、この問題ではレキシカルスコープに定数はありません。
レキシカルスコープに定数がない場合は、スーパークラスの探索を行います。

この問題では、クラスC2のスコープで定数を参照しています。
クラスC2のスーパークラスはクラスCdですので"100"が正解になります。
## 問題(selfの定義)
```
class Human
  NAME = "Unknown"

  def self.name
    const_get(:NAME)
  end
end

class Fukuzawa < Human
  NAME = "Yukichi"
end

puts Fukuzawa.name
```
### 解説
Class#nameはクラス名を文字列で返します。
Human#nameクラスはClass#nameをオーバーライドしているので、const_getが呼ばれます。

const_getは、selfに定義された定数を探索します。自クラスに定義がない場合は、メソッドと同様に探索を行います。

問題コードの5行目時点のインスタンスはFukuzawaクラスです。
よって、Human#nameはFukuzawaクラスのYukichiを返します。
## 問題(includeとextend)
```
class Company
  XXXX
  attr_reader :id
  attr_accessor :name
  def initialize id, name
    @id = id
    @name = name
  end
  def to_s
    "#{id}:#{name}"
  end
  YYYY
end

c1 = Company.new(3, 'Liberyfish')
c2 = Company.new(2, 'Freefish')
c3 = Company.new(1, 'Freedomfish')

print c1.between?(c2, c3)
print c2.between?(c3, c1)
```
### 解説
between?で値を比較するためには、Comparableをincludeする必要があります。

Comparableは比較に<=>を使用しています。

自作クラスの場合はオブジェクトIDが比較対象となります。

通常は、Comparable#<=>をオーバーライドします。

Fixnum#<=>(other)は以下の結果を返します。

selfがotherより大きい場合は、1を返します。

selfがotherと等しい場合は、0を返します。

selfがotherより小さい場合は、-1を返します。

extendはモジュールのインスタンスメソッドを特異メソッドとして追加します。

インスタンス変数からメソッドを参照することができなくなるので、エラーになります。

Sortableモジュールは存在しません。


## 問題()
```
module M
  @@val = 75

  class Parent
    @@val = 100
  end

  class Child < Parent
    @@val += 50
  end

  if Child < Parent
    @@val += 25
  else
    @@val += 30
  end
end

p M::Child.class_variable_get(:@@val)
```
### 解説
クラス変数はレキシカルに決定されますが、定数と同じスコープです。
```
module M
  CONST = 100
  @@val = 200
end

module M
  p CONST # 100 と表示されます
  p @@val # 200 と表示されます
end
```
ただし、クラス変数は上位のスコープ（外側）まで探索は行いません。（ここ大事）
```
module M
  CONST = 100
  @@val = 200
end

module M
  class C
    p CONST # 100 と表示されます
    p @@val # NameError
  end
end
```
また、Module#<はクラスの継承関係を比較することが出来ます。

継承をしているように見えますが、if文では継承関係を比較しています。

Module#<はレシーバーが引数の子孫である場合にtrueを返します。

問題にあるクラス変数はそれぞれ次のように計算されます。
```
module M
  @@val = 75

  class Parent
    @@val = 100
  end

  class Child < Parent
    @@val += 50 # @@val = 100 + 50
  end

  if Child < Parent
    @@val += 25 # @@val = 75 + 25
  else
    @@val += 30 # @@val = 75 + 30
  end
end

p M::Child.class_variable_get(:@@val)
```
この問題は150が表示されます。


## 問題(::演算子→トップレベル)
```
class Base
  CONST = "Hello, world"
end

class C < Base
end

module P
  CONST = "Good, night"
end

class Base
  prepend P
end

module M
  class C
    CONST = "Good, evening"
  end
end

module M
  class ::C
    def greet
      CONST
    end
  end
end

p C.new.greet
```
### 解説
::演算子が先頭にあるとトップレベルから定数の探索を行います。
モジュールMにあるクラスCはトップレベルにあるものを指します。

greetメソッドにあるCONSTはクラスCにはありませんが、スーパークラスにあるか探索を行います。
クラスBaseを継承していますので、"Hello, world"が表示されます。


## 問題()
```
次のプログラムは"Hello, world"と表示します。
同じ結果になる選択肢はどれですか（複数選択）

module M
  CONST = "Hello, world"
  def self.say
    CONST
  end
end

p M::say
```
### 解説
定数の定義はメモリ上にあるテーブルに管理されます。
モジュールMを別々に書いたとしてもテーブルを参照して値を取得できます。
```
module M
  CONST = "Hello, world"
end

module M
  def self.say
    CONST
  end
end

p M::say
```
instance_evalの引数に文字列を指定するとネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

M.instance_eval(<<-CODE)
  def say
    CONST
  end
CODE

p M::say
```
特異クラス定義のコンテキストでは、ネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

class << M
  def say
    CONST
  end
end

p M::say
```
module_evalの引数に文字列を指定するとネストの状態はモジュールMになります。

CONSTはモジュールMにありますので値を取得できます。
```
module M
  CONST = "Hello, world"
end

M.module_eval(<<-CODE)
  def self.say
    CONST
  end
CODE

p M::say
```
## 問題()
```
class C
  def initialize
    p self.class
  end
end

class C2 < C
end

C2.new
```
### 解説
selfはnewされたクラスのオブジェクトになります。
スーパークラスでselfを参照したとしても、C2.newのオブジェクトになります。
## 問題(usingは1度だけ)
```
class C
  def m1(value)
    100 + value
  end
end

module R1
  refine C do
    def m1
      super 50
    end
  end
end

module R2
  refine C do
    def m1
      super 100
    end
  end
end

using R1
using R2

puts C.new.m1
```
### 解説
同じメソッドに対してRefinementで再定義を2つのモジュールで行っています。

もし、usingを2行書いたとしても1つのメソッドで有効になる再定義は1つだけです。

最後に書いたusingから優先されます。

この問題ではusing R2が最後に有効化されたRefinementです。

有効になる再定義は1つだけですので、モジュールR2にあるsuperはクラスCにあるm1を呼び出します。

よって、super + 100は100 + 100となり200が表示されるのが正解です。
## 問題(evalのまとめ)
### 文字列を渡すパターン
**instance_eval**の引数に文字列を指定するとネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

M.instance_eval(<<-CODE)
  def say
    CONST
  end
CODE

p M::say
```

**module_eval**の引数に文字列を指定するとネストの状態はモジュールMになります。

CONSTはモジュールMにありますので値を取得できます。
```
module M
  CONST = "Hello, world"
end

M.module_eval(<<-CODE)
  def self.say
    CONST
  end
CODE

p M::say
```
### 解説

## 問題(includeはインスタンスメソッドをMix~in)
```
module M
  def class_m
    "class_m"
  end
end

class C
  include M
end

p C.methods.include? :class_m
```
### 解説
includeはModuleのインスタンスメソッドをMix-inするメソッドです。
C.methodsはCの特異メソッドを表示します。

よって、C#class_mはインスタンスメソッドです、C.methodsでは表示されません。
## 問題(extendは特異メソッドとして追加)
```
module M
  def class_m
    "class_m"
  end
end

class C
  extend M
end

p C.methods.include? :class_m
```
### 解説
extendは引数に指定したモジュールのメソッドを特異メソッドとして追加します。

問題のC.methods...は特異メソッドの一覧を取得します。
## 問題(正規表現)
```
p "Matz is my tEacher".scan(/[is|my]/).length
```
### 解説
問題で使用されている正規表現の説明は下記の通りです。

String#scanはマッチした部分文字列を配列で返します。
正規表現の[]は囲まれた文字1つ1つにマッチします。
|は正規表現ではORのメタ文字です。
今回は、|が[]に囲まれているため、これもマッチ対象になります。
問題のコードでscan(/[is|my]/)が返す配列は["i", "s", "m", "y"]になります。

```
 p "Matz is my tEacher".scan(/[is|my]/).length
4
=> 4
```
## 問題(sort)
```
class Company
  attr_reader :id
  attr_accessor :name
  def initialize id, name
    @id = id
    @name = name
  end
  def to_s
    "#{id}:#{name}"
  end
  def <=> other
    other.id <=> self.id
  end
end

companies = []
companies << Company.new(2, 'Liberyfish')
companies << Company.new(3, 'Freefish')
companies << Company.new(1, 'Freedomfish')

companies.sort!

companies.each do |e|
  puts e
end
```
### 解説
selfが右辺にあるときは降順になる。
## 問題(<< メソッド)
```
enum_char = Enumerator.new do |yielder|
  "apple".each_char do |chr|
    __(1)__
  end
end

array = enum_char.map do |chr|
  chr.ord
end

p array
```
### 解説
mapメソッドのブロックはEnumeratorオブジェクトをレシーバーとした場合にEnumerator::Yielderオブジェクトとなります。この問題のプログラム上では変数yielderを指します。

Enumerator::Yielderを評価するには、<<を呼び出します。
選択肢にある他のメソッドは実装されていません。
## 問題(特異メソッド？)
```
class C
  @val = 3
  attr_accessor :val
  class << self
    @val = 10
  end
  def initialize
    @val *= 2 if val
  end
end

c = C.new
c.val += 10

p c.val
```
### 解説
問題のコードは、13行目でc.valがnilになり、実行エラーになります。

2行目の@valはクラスインスタンス変数といい、特異メソッドからアクセスすることができます。

3行目の@valは特異クラスのクラスインスタンス変数です。

この値にアクセスするためには以下のようにアクセスします。
```
class << C
  p @val
end
```
13行目のc.valはattr_accessorよりアクセスされます。

initializeメソッドで初期化が行われていないため、nilが返されます。
## 問題()
```
class Array
  def succ_each(step = 1)
    return enum_for(:succ_each, step) unless block_given?

    each do |int|
      yield int + step
    end
  end
end

p [98, 99, 100].succ_each(2).map {|succ_chr| succ_chr.chr}

[101, 102, 103].succ_each(5) do |succ_chr|
  p succ_chr.chr
end
```
```
# 実行結果
["d", "e", "f"]
"j"
"k"
"l"
```
### 解説
ブロックを渡す場合と、チェーンを行う場合の両方を考慮する必要があります。
チェーンを行う場合はEnumeratorオブジェクトを作成する必要があります。作成に必要なメソッドはenum_forとto_enumです。

問題では、enum_forを使っていますので選択肢のうちto_enumを使っている選択肢が答えのひとつです。
ただし、to_enumは引数にメソッド名とそのメソッドに必要な引数を指定する必要があります。問題ではsucc_eachメソッドに引数2を渡していますのでEnumeratorオブジェクトを作成するときに必要になります。

また、Enumeratorオブジェクトはnewメソッドで作成することが出来ます。この問題ですと少し冗長ではありますが、全体的には次のとおりです。
```
class Array
  def succ_each(step = 1)
    unless block_given? # ブロックが無い場合は、オブジェクトを作成
      Enumerator.new do |yielder|
        each do |int|
          yielder << int + step
        end
      end
    else # ブロックがある場合の実装
      each do |int|
        yield int + step
      end
    end
  end
end
```
これも答えのひとつで、この問題ではto_enum(:succ_each, step)とEnumeratorオブジェクトを作成する選択肢が答えになります。

なお、チェーンした先で渡されたブロックを評価するためにはEnumerator::Yielderのオブジェクトを利用します。
オブジェクトに対して、<<を実行することでブロック内で評価した結果を受け取ることが出来ます。


## 問題(演算子の優先度)
```
v1 = 1 / 2 == 0
v2 = !!v1 or raise RuntimeError
puts v2 and false
```
### 解説
1行目では、Fixnumクラス同士の除算はFixnumクラスになります。

よって、0 == 0が評価され、v1はtrueになります。

2行目では、orは左辺が真であればその結果を返します。この時、右辺は評価されません。

左辺が偽であれば、右辺を評価しその結果を返します。

また、orは評価する優先順位が低い演算子です。

よって、優先順位が低いのでv2には!!v1の結果のtrueが入ります。

次に、!!v1 or raise RuntimeErrorが評価され、左辺が真であるため、左辺のみ評価されます。

3行目では、andは左辺が真であれば、右辺の結果を返します。左辺が偽であれば、左辺の結果を返します。

また、andは評価する優先順位が低い演算子です。

よって、優先順位が低いのでputs v2が評価されます。

演算子の優先順位を適切にするためには、括弧で式を区切ります。
## 問題(特異クラスの継承関係)
```
class C
  CONST = "Hello, world"
end

$c = C.new

class D
  class << $c
    def say
      CONST
    end
  end
end

p $c.say
```
### 解説
レキシカルスコープには定数はありません。その場合はスーパークラスを探索します。
特異クラスの継承関係にクラスCがありますので定数を見つけることができます。

参考：特異クラスの継承関係
```
[#<Class:#<C:0x007fa4741607e0>>, C, Object, Kernel, BasicObject]
```
## 問題()
```
def hoge(*args, &block)
  block.call(args)
end

hoge(1,2,3,4) do |*args|
  p args.length < 0 ? "hello" : args
end
```
### 解説
ブロックのargsここは配列らしい。。

## 問題(特異クラス)
```
class C
  def self._singleton
    class << C
      self
    end
  end
end

p C._singleton
```
### 解説
Object.singleton_classを利用すると特異クラスを取得することが出来ます。

特異クラスでselfを参照するとレシーバのオブジェクトがとれます。この選択肢では、クラスCが取得できます。
```
irb(main):256:0>例題
irb(main):257:1* class C
irb(main):258:2*   def self._singleton
irb(main):259:3*     class << C
irb(main):260:3*       self
irb(main):261:2*     end
irb(main):262:1*   end
irb(main):263:0> end
=> :_singleton
irb(main):264:0>
irb(main):265:0> p C._singleton
#<Class:C>
=> #<Class:C>
irb(main):266:0>選択肢1
irb(main):267:1* class C
irb(main):268:2*   def self._singleton
irb(main):269:3*     class << C
irb(main):270:3*       val = self
irb(main):271:2*     end
irb(main):272:2*     val
irb(main):273:1*   end
irb(main):274:0> end
=> :_singleton
irb(main):275:0>
irb(main):276:0> p C._singleton
(irb):272:in `_singleton': undefined local variable or method `val' for C:Class (NameError)
Did you mean?  @val
               eval
	from (irb):276:in `<main>'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/lib/ruby/gems/3.1.0/gems/irb-1.6.4/exe/irb:9:in `<top (required)>'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `load'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `<main>'
irb(main):277:0>選択肢2
irb(main):278:1* class C
irb(main):279:0> end
=> nil
irb(main):280:0>
irb(main):281:1* def C._singleton
irb(main):282:1*   self
irb(main):283:0> end
=> :_singleton
irb(main):284:0>
irb(main):285:0> p C._singleton
C
=> C
irb(main):286:0>選択肢3
irb(main):287:1* class C
irb(main):288:0> end
=> nil
irb(main):289:0>
irb(main):290:1* class << C
irb(main):291:2*   def _singleton
irb(main):292:2*     self
irb(main):293:1*   end
irb(main):294:0> end
=> :_singleton
irb(main):295:0>
irb(main):296:0> p C._singleton
C
=> C
irb(main):297:0>選択肢4
irb(main):298:1* class C
irb(main):299:0> end
irb(main):300:0> p C.singleton_class
#<Class:C>
=> #<Class:C>
irb(main):301:0>
```

## 問題(複素数)
```
次のコードを実行するとどうなりますか

val = 1i * 1i
puts val.class
```
### 解説
1iは複素数(Complex)のオブジェクトを表します。
Complex同士の演算はComplexを返します。


## 問題()
```
次のコードを実行するとどうなりますか

p [1,2,3,4].map(&self.method(:*))

```
### 解説
問題のselfはObjectクラスのインスタンスになります。
Objectクラスには*メソッドが定義されていないためエラーになります。

## 問題()
```
val = 100

def method(val)
  yield(15 + val)
end

_proc = Proc.new{|arg| val + arg }

p method(val, __(1)__)
```
### 解説
Procオブジェクトをメソッドで実行するにはブロックに変換する必要があります。
&をProcオブジェクトのプレフィックスにすることでブロックに変換することが出来ます。

また、to_procメソッドはProcオブジェクトを生成して返します。Procオブジェクトをレシーバにto_procを実行するとレシーバ自身が返ってきます。

この問題の答えは、&_proc.to_procと&_procです。
## 問題()
```
class Foo
  def initialize(num)
    @hoge = num
  end
end

num = (1..99).to_a.shuffle.first
foo = Foo.new(num)

__(1)__
```
### 解説
inspectは Object クラスに定義されており,String,Array,Hashなどのクラスにオーバーライドされています。レシーバーを文字列などに変換して表示してくれるためデバックなどに用いられます。
また、今回の設問のようにインスタンスオブジェクトに対してinspectメソッドを呼ぶことでインスタンス変数の値を表示することができます。

pメソッドにはinspectメソッドが用いられているため選択肢puts foo.inspectと同じ出力になります。
```
class Foo
  def initialize
    @num = 3
  end
end

sample = Foo.new
p sample
puts sample.inspect

#<Foo:0x00007fc2828f4700 @num=3>
#<Foo:0x00007fc2828f4700 @num=3>
```
```
irb(main):001:1* class Foo
irb(main):002:2*   def initialize(num)
irb(main):003:2*     @hoge = num
irb(main):004:1*   end
irb(main):005:0> end
=> :initialize
irb(main):006:0>
irb(main):007:0> num = (1..99).to_a.shuffle.first
irb(main):008:0> foo = Foo.new(num)
=> #<Foo:0x0000000104dafd68 @hoge=27>
irb(main):009:0> p foo
#<Foo:0x0000000104dafd68 @hoge=27>
=> #<Foo:0x0000000104dafd68 @hoge=27>
irb(main):010:0> puts foo.inspect
#<Foo:0x0000000104dafd68 @hoge=27>
=> nil
irb(main):011:0> puts foo.hoge
(irb):11:in `<main>': undefined method `hoge' for #<Foo:0x0000000104dafd68 @hoge=27> (NoMethodError)
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/lib/ruby/gems/3.1.0/gems/irb-1.6.4/exe/irb:9:in `<top (required)>'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `load'
	from /Users/hatajunnosuke/.rbenv/versions/3.1.0/bin/irb:25:in `<main>'
irb(main):012:0> puts foo.to_s
#<Foo:0x0000000104dafd68>
=> nil
```

## 問題()
```
class S
  def initialize
    puts "S#initialize"
  end
end

class C < S
  def initialize(*args)
    super
    puts "C#initialize"
  end
end

C.new(1,2,3,4,5)
```
### 解説
問題のコードはArgumentError: wrong number of arguments (5 for 0)が発生します。

superと呼び出した場合は、現在のメソッドと同じ引数が引き継がれます。
引数を渡さずにオーバーライドしたメソッドを呼び出す際はsuper()とします。
## 問題()
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
## 問題(Fiber)
```
fiber = Fiber.new do
  __(1)__ 'Hi, there!'
end

p __(2)__
```
### 解説
Fiberは軽量スレッドを提供します。

Fiber#resumeを実行するとFiber.yieldが最後に実行された行から再開するか、Fiber.newに指定したブロックの最初の評価を行います。

```
fiber = Fiber.new do
  Fiber.yield 'Hi, there!'
end

p fiber.resume
```
## 問題(includeのやり方)
```
irb(main):001:1* module M1
irb(main):002:0> end
=> nil
irb(main):003:0>
irb(main):004:1* module M2
irb(main):005:0> end
=> nil
irb(main):006:0>
irb(main):007:1* class C
irb(main):008:1*   include M1
irb(main):009:1*   include M2
irb(main):010:0> end
=> C
irb(main):011:0>
irb(main):012:0> p C.ancestors
[C, M2, M1, Object, PP::ObjectMixin, Kernel, BasicObject]
=> [C, M2, M1, Object, PP::ObjectMixin, Kernel, BasicObject]

irb(main):074:1* module M1
irb(main):075:0> end
=> nil
irb(main):076:0>
irb(main):077:1* module M2
irb(main):078:0> end
=> nil
irb(main):079:0>
irb(main):080:1* class C
irb(main):081:1*   include M1, M2
irb(main):082:0> end
=> C
irb(main):083:0>
irb(main):084:0> p C.ancestors
[C, M1, M2, Object, PP::ObjectMixin, Kernel, BasicObject]
=> [C, M1, M2, Object, PP::ObjectMixin, Kernel, BasicObject]
```
### 解説
一度にincludeするか、分けてincludeするかで順番が変わる！！
## 問題(ネストの継承)
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

## 問題(class_evalのブロックと文字の違い)
```
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

ブロックならネストは変わらない、文字ならselfになる。

なお、呼び出しはselfから呼び出す。定数を探す際はレキシカルになる。

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
## 問題()
```
array = ["a", "b", "c"].freeze

array.each do |chr|
  chr.upcase!
end

p array
```
### 解説
これは個別に破壊的なので成立する
## 問題(サブモジュール)
```
module M1
  def method_1
    __method__
  end
end

class C
  include M1
end

p C.new.method_1

module M2
  def method_2
    __method__
  end
end

module M1
  include M2
end

p C.new.method_2

```
### 解説
クラスはサブモジュールを継承ツリーに追加するには条件があります。

サブモジュールを含めるには、クラスにインクルードする前にサブモジュールを継承ツリーに追加する必要があります。後からモジュールでincludeしてもダメですよ。

次のサンプルプログラムでは、クラスCにモジュールM1をインクルードする前に、サブモジュールM2を継承関係に含めているのでメソッドを呼び出すことができます。
```
module M1
  def method_1
    __method__
  end
end

module M2
  def method_2
    __method__
  end
end

module M1
  include M2
end

class C
  include M1 # モジュールM2が既に継承関係にある
end

p C.new.method_1 # method_1と表示される
p C.new.method_2 # method_2と表示される
```
問題のプログラムでは、メソッドmethod_2は継承ツリーにモジュールM2がないため呼び出すことができず、例外が発生します。

サブモジュールをモジュールの継承ツリーに追加してもクラスCの継承ツリーは更新されません。
```
module M1
  def method_1
    __method__
  end
end

class C
  include M1
end

p C.new.method_1

module M2
  def method_2
    __method__
  end
end

module M1
  include M2 # サブモジュールM2を追加しても、クラスCの継承ツリーは更新されない
end

p C.ancestors # [C, M1, Object, Kernel, BasicObject] と表示される
```
## 問題(includeのself)
```
irb(main):096:1* module M
irb(main):097:2*   def self.class_m
irb(main):098:2*     "M.class_m"
irb(main):099:1*   end
irb(main):100:0> end
=> :class_m
irb(main):101:0>
irb(main):102:1* class C
irb(main):103:1*   include M
irb(main):104:0> end
=> C
irb(main):105:0>
irb(main):106:0> p C.methods.include? :class_m
false
=> false
irb(main):107:0> p M.methods.include? :class_m
true
```
### 解説
問題コードの注意すべき点は以下の通りです。

includeはModuleのインスタンスメソッドをMix-inするメソッドです。

def self.class_mと宣言すると、特異クラスのメソッドになります。

C.methodsはCの特異メソッドを表示します。

よって、Cにはclass_mが追加されません。

Mのメソッドとして追加されたという認識
## 問題()
```
class IPAddr
  include Enumerable

  def initialize(ip_addr)
    @ip_addr = ip_addr
  end

  def each
    return __(1)__ unless block_given?

    @ip_addr.split('.').each do |octet|
      yield octet
    end
  end
end

addr = IPAddr.new("192.10.20.30")
enum = addr.each

p enum.next # 192と表示される
p enum.next # 10と表示される
p enum.next # 20と表示される
p enum.next # 30と表示される
```
### 解説
Enumerableをインクルードした場合は、eachメソッドを実装する必要があります。

ブロックが渡されない場合でも、Enumeratorオブジェクトを返すようにして外部イテレータとしても使えるようにします。

Enumeratorオブジェクトを作成するメソッドはenum_for、またはto_enumです。
## 問題()
```

```
### 解説

## 問題()
```

```
### 解説


## ~eval
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

## 問題()
```
次のプログラムは"Hello, world"と表示します。
同じ結果になる選択肢はどれですか（複数選択）

module M
  CONST = "Hello, world"
  def self.say
    CONST
  end
end

p M::say
```
### 解説
定数の定義はメモリ上にあるテーブルに管理されます。
モジュールMを別々に書いたとしてもテーブルを参照して値を取得できます。
```
module M
  CONST = "Hello, world"
end

module M
  def self.say
    CONST
  end
end

p M::say
```
instance_evalの引数に文字列を指定するとネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

M.instance_eval(<<-CODE)
  def say
    CONST
  end
CODE

p M::say
```
特異クラス定義のコンテキストでは、ネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

class << M
  def say
    CONST
  end
end

p M::say
```
module_evalの引数に文字列を指定するとネストの状態はモジュールMになります。

CONSTはモジュールMにありますので値を取得できます。
```
module M
  CONST = "Hello, world"
end

M.module_eval(<<-CODE)
  def self.say
    CONST
  end
CODE

p M::say
```

## 問題(evalのまとめ)
### 文字列を渡すパターン
**instance_eval**の引数に文字列を指定するとネストの状態はモジュールMの特異クラスになります。

CONSTはモジュールMにのみありますので、例外が発生します。
```
module M
  CONST = "Hello, world"
end

M.instance_eval(<<-CODE)
  def say
    CONST
  end
CODE

p M::say
```

**module_eval**の引数に文字列を指定するとネストの状態はモジュールMになります。

CONSTはモジュールMにありますので値を取得できます。
```
module M
  CONST = "Hello, world"
end

M.module_eval(<<-CODE)
  def self.say
    CONST
  end
CODE

p M::say
```
### 解説

## 問題(module_evalのブロック)
```
mod = Module.new

mod.module_eval do
  EVAL_CONST = 100
end

puts "EVAL_CONST is defined? #{mod.const_defined?(:EVAL_CONST)}"
puts "EVAL_CONST is defined? #{Object.const_defined?(:EVAL_CONST)}"
```
### 解説
定数のスコープはレキシカルに決定されます。

ブロックはネストの状態を変更しないので、module_evalのブロックで定義した定数はこの問題ではトップレベルで定義したことになります。

定数EVAL_CONSTはトップレベルで定義していることになりますので、Objectクラスに定数あることが確認することが出来ます。

また、Moduleクラスのインスタンスには直接、定数は定義されていませんが継承関係を探索して参照することが出来ます。

const_defined?メソッドは第2引数に継承関係を探索するか指定出来るため、この問題では探索を行うかによって結果が変わります。
```
mod = Module.new

mod.module_eval do
  EVAL_CONST = 100
end

puts Object.const_defined? :EVAL_CONST # trueと表示される
puts mod.const_defined? :EVAL_CONST # trueと表示される

# 第2引数にfalseを指定すると継承関係まで探索しない
puts mod.const_defined? :EVAL_CONST, false # falseと表示される
```
この問題では指定してない（デフォルト値true）ため探索を行い、定数をどちらも見つけることが出来ます。

結果は次のとおりです。
```
EVAL_CONST is defined? true
EVAL_CONST is defined? true
```
## ensure説の評価値
```
irb(main):022:1* def m
irb(main):023:2*   begin
irb(main):024:2*     1
irb(main):025:2*   rescue
irb(main):026:2*     2
irb(main):027:2*   else
irb(main):028:2*     3
irb(main):029:2*   ensure
irb(main):030:2*     4
irb(main):031:1*   end
irb(main):032:0> end
=> :m
irb(main):033:0>
irb(main):034:0> p m
3
=> 3
```
ensureは無視される

## Rubyのコマンドラインオプション
例があるのでわかりやすい

https://peakp.hatenablog.com/entry/2015/05/06/213912
## 特殊変数
https://portaltan.hatenablog.com/entry/2015/10/26/181540
## 組み込み

## 基礎問題
- 6/9.  20/30
## 演習問題
- 6/10  29/50→ヤバめ
