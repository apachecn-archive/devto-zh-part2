# 一切都是物体！

> 原文：<https://dev.to/craigbrad/everything-is-an-object-5184>

**一切都是物体**。你可能以前听过这个短语，但是它是什么意思呢？

### 什么是物体？

一个对象*指的是一个类的特定实例，它们有自己的方法或过程和数据变量*，这意味着**一切都是一个类的实例**。

### 类型是对象

在静态类型语言中，我们可以定义不同类型的变量，这些变量不是对象，它们没有方法，也没有任何属性。我们可以说动态类型语言将类型定义为类，所以举例来说，如果你有一个字符串，它就是`String`类的一个实例。

让我们来看看 ruby 中的一些例子:

#### 真/假/无

```
# Define a boolean and store as a variable my_bool
my_bool = true # => true 

# Can call the class method to return the class of the object
my_bool.class # => TrueClass

# The superclass of TrueClass is Object
TrueClass.superclass # => Object

# my_bool is an object
my_bool.is_a?(Object) # => true

my_bool = false # => false
my_bool.class # => FalseClass
FalseClass.superclass # => Object
my_bool.is_a?(Object) # => true

my_nil = nil # => nil
my_nil.class # => NilClass
NilClass.superclass # => Object
my_nil.is_a?(Object) # => true 
```

#### 字符串

```
my_string = "Some string" # => "Some string"
my_string.class # => String
String.superclass # => Object
my_string.is_a?(Object) # => true 
```

好的，很好，字符串、布尔值和 nil 都是对象，但是其他数据类型呢？

#### 更多数据类型

```
# Define an array of values with different types
my_array = [1, "two", :three, { "four" => 4 }, [5]]

# List of each objects type class
classes = my_array.map(&:class) # => [Integer, String, Symbol, Hash, Array]

# Every type class eventually inherits from object
classes.map(&:superclass) # => [Numeric, Object, Object, Object, Object]

# An Integer has a superclass of Numeric, Numeric has a superclass of Object
Numeric.superclass # => Object

# All values in my_array are objects
my_array.map { |val| val.is_a?(Object) } # => [true, true, true, true, true] 
```

所以你创建的每个变量都是一个对象，这可能并不奇怪。然而，班级呢？它们是物体吗？是的(至少在 ruby 中)。

### 类是对象

Ruby 中的类是一级对象——每一个都是 class Class 的实例。

所以让我们来看看这意味着什么-
当我们在 ruby 中创建一个类时，我们通常使用下面的语法:

```
# Define new class Foo
class Foo
end

# Foo is an object
Foo.is_a?(Object) # => true 
```

但是在引擎盖下到底发生了什么呢？如果我们使用不同的语法来定义一个类可能会更有意义:

```
# Define new class Foo
Foo = Class.new
Foo.is_a?(Object) # => true 
```

这和上面的传统方法做的完全一样，但是我认为这使得发生的事情更加清楚。每当您创建一个新类时，您实际上是在创建一个新的`Class`实例。你在创造物体。然后将该对象赋给一个常数，因为您只需要它的一个实例。通过调用`Module`上的`constants`方法，可以看到所有的常量。请参见下面的常量列表。

```
Module.constants # => [:String, :Float, :NilClass, :Hash, :Array,
# :Integer, :TrueClass, :FalseClass, :Numeric, :Fixnum, :Range, 
# :Foo, :BasicObject, :Object, :Module, :Class, :Symbol] 
```

您可以看到我们之前创建的 Foo 类已经被添加到这个常量列表中。这个列表中的每个常量都是一个对象。

### 对象 Id

ruby 中的每个对象都有一个唯一的 id，可以通过调用`object_id`来访问。使用这种方法，我们可以看到哪些对象具有相同的 id，并且也有一些有趣的结果。常数将保持相同的 id，直到执行停止。当程序再次执行时，不能保证常量会保持相同的 id。然而，这条规则也有例外，我现在就来讨论一下。

#### 真/假/无

```
true1 = true
true2 = true
true1.object_id # => 20
true2.object_id # => 20

false1 = false
false2 = false
false1.object_id # => 0
false2.object_id # => 0

nil1 = nil
nil1 = nil
nil1.object_id # => 8
nil2.object_id # => 8 
```

`true`、`false`和`nil`是特殊情况，每次执行程序时，它们将保持相同的`object_id`。这些值中的每一个都只有一个实例。

#### 整数

```
int_a = 1
int_b = 1
int_a.object_id # => 3
int_b.object_id # => 3

int_a = 2
int_b = 2
int_a.object_id # => 5
int_b.object_id # => 5

int_a = 3
int_b = 3
int_a.object_id # => 7
int_b.object_id # => 7 
```

ruby 中的每个整数都有自己唯一的 id，这一点永远不会改变。每当你引用一个变量`234`，你就在引用完全相同的对象。您可能已经注意到了整数的`object_id`中的一种模式。如果您将整数左移一位并加一，您将获得对象 id。反之亦然，如果你将对象 id 向右移位，你将得到整数。我相信这是性能原因。

#### 符号

```
my_sym1 = :foo
my_sym2 = :foo
my_sym1.object_id # => 1280988
my_sym2.object_id # => 1280988 
```

符号是不可改变的。第一次创建符号`:foo`时，它将创建一个新对象，并将其存储在一个列表中，您可以通过`Symbol.all_symbols`访问该列表。第二次，我们在列表中已经有了一个`:foo`符号，因此它将引用这个。符号创建一次，引用多次，不能更改。

#### 字符串

```
my_string1 = "foo"
my_string2 = "foo"
my_string1.object_id # => 70093438879580
my_string2.object_id # => 70093438872080 
```

字符串是可变的，因此当你创建两个相等的字符串时，你会得到不同的对象。虽然我们已经创建了两个变量，它们都有相同的值`"foo"`,但是它们是不同的对象。

### 所以一切都是物体？

当我们说一切都是对象时，一切的部分有点模糊。方法、块和操作符不是对象。方法和块的界限有点模糊，因为它们可以被解释为对象。

#### 方法

```
# Declare a new method foo
def foo
  puts "test"
end

# Get a wrapped version of foo
my_method = method(:foo)

# my_method is an instance of Method
my_method.class # => Method

# Method has a superclass of Object
Method.superclass # => Object

# Can invoke the method using call
my_method.call # => "test" 
```

不清楚方法实际上是否是对象。它们的行为类似于对象，但是您永远不能直接访问该方法。您总是会得到该方法的一个包装版本，其行为类似于一个闭包。我很想说方法不是对象。

### 总结

总而言之，ruby 中的几乎所有东西都是对象。我们使用的每种数据类型都是一个类，而类是对象。甚至`Object`类也是一个对象。字符串、整数、浮点、散列、数组、符号、类、模块、错误等等都是对象。打开一个`irb`控制台，自己看看，有一些非常有趣的观察结果要做。