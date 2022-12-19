# Ruby 中的运行时自省

> 原文：<https://dev.to/amrrbakry/runtime-introspection-in-ruby-2po1>

Ruby 提供了各种各样的*方法*，允许你询问一个对象关于它的能力(它响应哪些消息/方法？)，它的变量和常量，以及它的背景故事(对象的类和祖先)。

## **方法内省**

让我们从询问一个对象关于它的非私有`methods` :
开始

```
str = 'a string'
str.methods

# => [:include?, :%, :*, :+, :unicode_normalize, :to_c, :unicode_normalize!, :unicode_normalized?, :count, ...] 
```

Enter fullscreen mode Exit fullscreen mode

`methods`返回代表接收方字符串对象 *`str`* 的公共和受保护方法名称的符号数组。

我们也可以询问对象:

*   `private_methods`
*   `public_methods`
*   `protected_methods`
*   `singleton_methods`

默认情况下，Ruby 会查找并列出对象的类及其祖先类和模块中的所有方法。如果你只想列出对象类中的方法，你可以传递参数 *false* 或 *nil* 给这些方法。

```
str = 'a string'
str.public_methods(false)

# public methods defined in String only
# => [:include?, :%, :*, :+, :unicode_normalize, :to_c, :unicode_normalize!, :unicode_normalized?, :count, ...] 
```

Enter fullscreen mode Exit fullscreen mode

有时，你只需要知道一个对象是否知道一个特定的方法，这就是我们使用恰当命名的`respond_to?` :
的时候

```
str = 'a string'
str.respond_to?(:include?)
# => true
str.respond_to?(:first)
# => false 
```

Enter fullscreen mode Exit fullscreen mode

## **变量和常数自省**

同样，我们可以查询一个关于`instance_variables`、`class_variables`和`constants`的类。

```
class Cat
  @@cats_count = 2

  CATS = ['Luna', 'Milo']

  attr_accessor :name, :age

  def initialize(name)
    @name = name
  end
end

cat = Cat.new('Max')
cat.age = 2

cat.instance_variables
# => [:@name, :@age]

cat.class.class_variables
# => [:@@cats_count]

cat.class.constants
# => [:CATS] 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有列出`local_variables`和`global_variables`的顶级方法。

```
# irb session
>> local_variables
# => [:_] 
```

Enter fullscreen mode Exit fullscreen mode

这个*下划线*局部变量实际上是一个非常有趣的变量；它总是保存最后一个求值的表达式:

```
Cat.new('Milo')
# => #<Cat:0x00000002415440 @name="Milo">
cat = _
# => #<Cat:0x00000002415440 @name="Milo"> 
```

Enter fullscreen mode Exit fullscreen mode

## **物体的背景故事**

最后，我们来问一下这个物体的`class`、`superclass`、`ancestors`和

```
cats = ['luna', 'milo']
cats.class
# => Array

cats.class.superclass
# => Object

cats.class.ancestors
# => [Array, Enumerable, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

`ancestors`返回一个列表，包括它的接收者(Array)和数组中包含的模块(Enumerable，Kernel)，数组的超类(Object)，最后是对象的超类(BasicObject)。

但不是这样。还有更方便的方法来询问对象的类...

*   `is_a?`
*   `kind_of?`
*   `instance_of?`

这三个谓词方法也可以帮助我们查询:

```
cats = ['luna', 'milo']
cats.is_a?(Array)
# => true 
```

Enter fullscreen mode Exit fullscreen mode

这里要注意的一个重要区别是，只有当接收者对象是类的直接实例而不是子类的实例时，`instance_of?`才会返回 true

```
class A; end
class B < A; end

b = B.new
# => #<B:0x00000001fac638>
b.kind_of?(A)
# true
b.instance_of?(A)
# false 
```

Enter fullscreen mode Exit fullscreen mode

这仅仅是 ruby 内省能力的一瞥，但是你可以从你的对象中学到很多有趣的东西！

*这篇文章也可以在[媒体](https://medium.com/@amrrbakry/runtime-introspection-in-ruby-b2d718ec704f)上找到。*