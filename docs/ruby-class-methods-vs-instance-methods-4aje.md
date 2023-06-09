# Ruby:类方法与实例方法

> 原文：<https://dev.to/adamlombard/ruby-class-methods-vs-instance-methods-4aje>

在 Ruby 中，*方法*为对象提供功能。一个*类方法*为类本身提供功能，而一个*实例方法*为类的一个实例提供功能。

考虑下面的 Ruby 类:

```
class SayHello
  def self.from_the_class
    "Hello, from a class method"
  end

  def from_an_instance
    "Hello, from an instance method"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这将产生以下结果:

```
>> SayHello.from_the_class
=> "Hello, from a class method"

>> SayHello.from_an_instance
=> undefined method `from_an_instance' for SayHello:Class

>> hello = SayHello.new
>> hello.from_the_class
=> undefined method `from_the_class' for #<SayHello:0x0000557920dac930>

>> hello.from_an_instance
=> "Hello, from an instance method" 
```

Enter fullscreen mode Exit fullscreen mode

我们不能在类本身上调用实例方法，也不能在实例上直接调用类方法。

Railstips 有一篇很好的文章，提供了更多的细节，并讨论了创建类方法和实例方法的替代方法。

* * *

参见: [Python:类对实例对静态方法](https://dev.to/adamlombard/python-class-vs-instance-vs-static-methods-2cd0)