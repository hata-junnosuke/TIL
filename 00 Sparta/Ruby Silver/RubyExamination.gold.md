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
