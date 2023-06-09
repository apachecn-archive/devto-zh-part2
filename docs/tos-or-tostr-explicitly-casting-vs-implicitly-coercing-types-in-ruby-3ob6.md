# #to_s 还是#to_str？Ruby 中显式强制类型与隐式强制类型的比较

> 原文：<https://dev.to/appsignal/tos-or-tostr-explicitly-casting-vs-implicitly-coercing-types-in-ruby-3ob6>

类型强制是将一个对象的类型及其值改变为另一种类型。比如用`#to_s`把整数换成字符串，或者用`#to_i`把浮点数换成整数。有些对象实现的可能不太为人所知的`#to_str`和`#to_int`方法乍看起来是一样的，但是有一些不同。

在本期 AppSignal academy 中，我们将深入探讨 Ruby 中的显式类型转换和隐式强制类型转换，同时简要介绍类型转换参与者。我们将讨论这两种方法之间的区别，并讨论如何使用它们。

让我们先来看看在 Ruby 中，我们通常是如何使用显式强制转换助手将值强制转换为不同类型的。

## 显式强制转换助手

最常见的选角助手有`#to_s`、`#to_i`、`#to_a`和`#to_h`。这些是显式转换方法。它们帮助我们轻松地将一个值从一种类型转换为另一种类型。

明确的帮助者带来了明确的承诺。每当在一个对象上调用`#to_s`时，*总是*返回一个字符串，即使该对象没有很好地转换成字符串。这就像让迈克尔·基顿扮演蝙蝠侠一样。你会得到一个蝙蝠侠，即使喜剧演员并不特别适合这个角色。

Ruby 在 Ruby 标准库中的几乎所有基本对象上都提供了这些助手方法。

```
:foo.to_s # => "foo"
10.0.to_i # => 10
"10".to_i # => 10 
```

Enter fullscreen mode Exit fullscreen mode

这些方法，尤其是`#to_s`，在 Ruby 中的大多数基本类型上都实现了。虽然强制转换几乎总是返回值，但结果可能不是我们所期望的。

```
"foo10".to_i          # => 0
[1, 2, 3].to_s        # => "[1, 2, 3]"
{ :foo => :bar }.to_s # => "{:foo=>:bar}"
{ :foo => :bar }.to_a # => [[:foo, :bar]]
Object.to_s           # => "Object"
Object.new.to_s       # => "#<Object:0x00007f8e6d053a90>" 
```

Enter fullscreen mode Exit fullscreen mode

调用`#to_s`、`#to_i`、`#to_a`和`#to_h`助手会将任何值强制给所选类型。不管值发生了什么变化，它们都返回被强制转换的类型的表示形式。

## 隐含强制手段

对行为与我们要转换到的类型不同的值调用类型转换方法可能会导致错误或数据丢失。Ruby 还提供了隐式强制方法，这些方法只在对象的行为类似于类型时才返回值。通过这种方式，我们可以确保值的行为像我们想要的类型。这些隐性强制方式分别是`#to_str`、`#to_int`、`#to_ary`和`#to_hash`。

隐含的强制就像让伦纳德·尼莫伊扮演除了斯波克以外的任何角色。如果角色离斯波克足够近，它们会起作用，但如果离得不够近，它们就会失效。`#to_str`助手*试图将*转换成一个字符串，但是如果对象没有实现这个方法并且不能被隐式强制，那么就会引发一个`NoMethodError`。

```
10.to_int                           # => 10
10.0.to_int                         # => 10
require "bigdecimal"
BigDecimal.new("10.0000123").to_int # => 10

# Unsuccessful coercions
"10".to_int             # => NoMethodError
"foo10".to_int          # => NoMethodError
[1, 2, 3].to_str        # => NoMethodError
{ :foo => :bar }.to_str # => NoMethodError
{ :foo => :bar }.to_ary # => NoMethodError
Object.to_str           # => NoMethodError
Object.new.to_str       # => NoMethodError 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，Ruby 现在更加严格了，它不强制请求的类型。如果强制是不可能的，就不会在对象上实现`#to_*`方法，调用它会引发一个`NoMethodError`。

当使用隐式强制时，例如`#to_str`，我们要求函数返回一个字符串对象，前提是原始类型也像字符串一样。因此，`#to_str`只在 Ruby 标准库中的 String 上实现。

### Ruby 如何使用隐式强制

除了在强制过程中更精确地要求什么之外，隐式强制还有什么用处呢？原来 Ruby 在相当多的场景中使用了隐式强制。例如，当用`+`组合对象时。

```
name = "world!"
"Hello " + name # => "Hello world!"

# Without #to_str
class Name
  def initialize(name)
    @name = name
  end
end
"Hello " + Name.new("world!") # => TypeError: no implicit conversion of Name into String 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们看到 Ruby 抛出了一个`TypeError`，因为它不能进行从`Name`类型到`String`类型的隐式转换。

如果我们在类上实现`#to_str`，Ruby 知道如何强制`Name`类型。

```
# With #to_str
class Name
  def to_str
    @name
  end
end
"Hello " + Name.new("world!") # => "Hello world!" 
```

Enter fullscreen mode Exit fullscreen mode

数组和`#to_ary`也是如此。

```
class Options
  def initialize
    @internal = []
  end

  def <<(value)
    @internal << value
  end
end

options = Options.new
options << :foo
[:some_prefix] + options # => TypeError: no implicit conversion of Options into Array

class Options
  def to_ary
    @internal
  end
end
[:some_prefix] + options # => [:some_prefix, :foo] 
```

Enter fullscreen mode Exit fullscreen mode

但是`#to_ary`用在更多的场景。我们可以用它来把一个数组分解成独立的变量。

```
options = Options.new
options << :first
options << :second
options << :third
first, second, third = options
first  # => :first
second # => :second
third  # => :third 
```

Enter fullscreen mode Exit fullscreen mode

它还将对象转换为块参数。

```
[options].each do |(first, second)|
  first # => :first
  second # => :second
end 
```

Enter fullscreen mode Exit fullscreen mode

使用隐式强制方法的场景更多，比如`#to_hash`和`**`。在将值传递给`parse_options`方法之前，这将值强制转换为带有`#to_hash`的散列值。

```
class Options
  def to_hash
    # Create a hash from the Options Array
    Hash[*@internal]
  end
end

def parse_options(opts)
  opts
end

options = Options.new
options << :key
options << :value
parse_options(**options) # => {:key=>:value} 
```

Enter fullscreen mode Exit fullscreen mode

## 强制执行类型

当类型是未知类型并且我们希望确保得到正确的类型时，Ruby 还提供了更具弹性的强制方法。每个基本类型都有一个([`String(...)`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-String)[`Integer(...)`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-Integer)[`Float(...)`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-Float)[`Array(...)`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-Array)[`Hash(...)`](http://ruby-doc.org/core-2.5.1/Kernel.html#method-i-Hash)等)。).

```
String(self)       # => "main"
String(self.class) # => "Object"
String(123456)     # => "123456"
String(nil)        # => ""

Integer(123.999)   # => 123
Integer("0x1b")    # => 27
Integer(Time.new)  # => 1204973019
Integer(nil)       # => TypeError: can't convert nil into Integer 
```

Enter fullscreen mode Exit fullscreen mode

`String(...)`方法首先尝试对值调用`#to_str`，当失败时，它调用它的`#to_s`方法。并不是所有的对象都定义了一个`#to_str`方法，因此同时使用隐式强制(`#to_str`)和显式(`#to_s`)转换方法会增加字符串转换成功的机会，并且您会得到想要的值。通过首先调用隐式强制，我们更有可能得到具有相同值但属于强制类型的结果，而不是类似于`"#<Object:0x00007f8e6d053a90>"`的结果。

```
class MyString
  def initialize(value)
    @value = value
  end

  def to_str
    @value
  end
end

s = MyString.new("hello world")
s.to_s    # => "#<MyString:0x...>"
s.to_str  # => "hello world"
String(s) # => "hello world" 
```

Enter fullscreen mode Exit fullscreen mode

您应该只为行为类似于 to be compressed 类型的对象实现隐式转换方法，例如，为您自己的 String 类实现`#to_str`。

除了首先尝试隐式强制之外，`String(...)`助手还检查返回的类型。`#to_str`只是一个可以返回任何类型值的方法，甚至是非字符串。为了确保我们得到所请求类型的值，如果类型不匹配，`String(...)`会抛出一个`TypeError`。

```
class MyString
  def to_str
    nil
  end
end

s = MyString.new("hello world")
s.to_s    # => "#<MyString:0x...>"
s.to_str  # => nil
String(s) # => "#<MyString:0x...>" 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们可以看到 Ruby 忽略了`#to_str`的结果，因为它返回了`nil`，这不是 String 类型的。相反，它退回到`#to_s`结果。

如果`#to_s`也返回`nil`，因此不是正确的类型，`String(...)`将引发一个`TypeError`。

```
class MyString
  def to_str
    nil
  end

  def to_s
    nil
  end
end

s = MyString.new("hello world")
s.to_s    # => nil
s.to_str  # => nil
String(s) # => TypeError: can't convert MyString to String (MyString#to_s gives NilClass) 
```

Enter fullscreen mode Exit fullscreen mode

虽然它们在强制类型强制方面可能更可靠，但请注意强制转换辅助方法(`String(...)`、`Integer(...)`等)。)通常有点慢，因为它们需要对给定值执行更多的检查。

## 总之

当您想要确保您正在为一个对象处理正确的数据类型时，类型强制是一个有用的过程。在这篇文章中，我们更新了我们对像`#to_s`、`#to_i`、`#to_a`和`#to_h`这样的显式造型助手的知识。我们还看了像`#to_str`、`#to_int`、`#to_ary`和`#to_hash`这样的隐式助手有用的例子，以及 Ruby 本身如何使用它们。

我们希望您发现这种类型强制概述是有用的，以及您是如何发现演员类型转换类比的。像往常一样，如果有你想要我们报道的话题，请告诉我们。如果你有任何问题或意见，不要犹豫，留下评论。