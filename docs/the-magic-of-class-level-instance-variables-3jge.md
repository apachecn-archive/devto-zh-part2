# 类级实例变量的魔力

> 原文：<https://dev.to/appsignal/the-magic-of-class-level-instance-variables-3jge>

在之前的 Ruby Magic 的[中，我们发现了如何通过覆盖它的`.new`方法来可靠地将模块注入到类中，允许我们用额外的行为包装方法。](https://blog.appsignal.com/2018/08/07/ruby-magic-changing-the-way-ruby-creates-objects.html)

这一次，我们更进一步，将该行为提取到它自己的模块中，以便我们可以重用它。我们将构建一个为我们处理类扩展的`Wrappable`模块，我们将在这个过程中学习所有关于类级实例变量的知识。让我们开始吧！

## 介绍`Wrappable`模块

为了在初始化时用模块包装对象，我们必须让类知道使用什么包装模型。让我们首先创建一个简单的`Wrappable`模块，该模块提供一个`wrap`方法，该方法将给定的模块推入一个定义为类属性的数组中。此外，我们重新定义了在前一篇文章中讨论过的`new`方法。

```
module Wrappable 
  @@wrappers = []

  def wrap(mod)
    @@wrappers << mod 
  end 

  def new(*arguments, &block)
    instance = allocate
    @@wrappers.each { |mod| instance.singleton_class.include(mod) } 
    instance.send(:initialize, *arguments, &block)
    instance
  end 
end 
```

Enter fullscreen mode Exit fullscreen mode

为了给类添加新的行为，我们使用`extend`。`extend`方法将给定的模块添加到类中。然后这些方法就变成了类方法。要添加一个模块来包装这个类的实例，我们现在可以调用`wrap`方法。

```
module Logging 
  def make_noise 
    puts "Started making noise"
    super 
    puts "Finished making noise"
  end
end

class Bird
  extend Wrappable 

  wrap Logging

  def make_noise
    puts "Chirp, chirp!"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过创建一个新的`Bird`实例并调用`make_noise`方法来尝试一下。

```
bird = Bird.new 
bird.make_noise
# Started making noise
# Chirp, chirp!
# Finished making noise 
```

Enter fullscreen mode Exit fullscreen mode

太好了！它像预期的那样工作。然而，一旦我们用`Wrappable`模块扩展了第二个类，事情就变得有点奇怪了。

```
module Powered
  def make_noise 
    puts "Powering up"
    super 
    puts "Shutting down" 
  end 
end 

class Machine 
  extend Wrappable 

  wrap Powered

  def make_noise 
    puts "Buzzzzzz" 
  end 
end

machine = Machine.new 
machine.make_noise
# Powering up
# Started making noise
# Buzzzzzz
# Finished making noise
# Shutting down

bird = Bird.new 
bird.make_noise
# Powering up
# Started making noise
# Chirp, chirp!
# Finished making noise
# Shutting down 
```

Enter fullscreen mode Exit fullscreen mode

即使`Machine`没有被`Logging`模块包装，它仍然输出日志信息。更糟糕的是——甚至这只鸟现在也在上下电。那不可能是对的，是吗？

这个问题的根源在于我们存储模块的方式。类变量`@@wrappables`是在`Wrappable`模块上定义的，每当我们添加一个新模块时都会用到，不管`wrap`用在哪个类中。

当查看在`Wrappable`模块和`Bird`和`Machine`类上定义的类变量时，这一点更加明显。虽然`Wrappable`定义了一个类方法，但这两个类没有。

```
Wrappable.class_variables # => [:@@wrappers]
Bird.class_variables # => []
Machine.class_variables # => [] 
```

Enter fullscreen mode Exit fullscreen mode

要解决这个问题，我们必须修改实现，使其使用实例变量。然而，这些不是`Bird`或`Machine`实例上的变量，而是类本身的实例变量。

> ## 在 Ruby 中，类只是对象
> 
> 这一开始肯定有点令人难以置信，但仍然是一个需要理解的非常重要的概念。类是`Class`的实例，编写`class Bird; end`等同于编写`Bird = Class.new`。让事情更加混乱的是`Class`继承了`Module`，而【】又继承了`Object`。因此，类和模块具有与任何其他对象相同的方法。我们在类上使用的大多数方法(比如`attr_accessor`宏)实际上都是`Module`的实例方法。

## 在类上使用实例变量

让我们将`Wrappable`实现改为使用实例变量。为了保持简洁，我们引入了一个`wrappers`方法，当实例变量已经存在时，该方法要么建立数组，要么返回现有的数组。我们还修改了`wrap`和`new`方法，以便它们利用新的方法。

```
module Wrappable
  def wrap(mod)
    wrappers << mod
  end

  def wrappers
    @wrappers ||= []
  end

  def new(*arguments, &block)
    instance = allocate
    wrappers.each { |mod| instance.singleton_class.include(mod) }
    instance.send(:initialize, *arguments, &block)
    instance
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当我们检查模块和两个类上的实例变量时，我们可以看到`Bird`和`Machine`现在都维护它们自己的包装模块集合。

```
Wrappable.instance_variables #=> []
Bird.instance_variables #=> [:@wrappers]
Machine.instance_variables #=> [:@wrappers] 
```

Enter fullscreen mode Exit fullscreen mode

毫不奇怪，这也解决了我们之前观察到的问题——现在，两个类都用它们自己的独立模块包装起来了。

```
bird = Bird.new 
bird.make_noise
# Started making noise
# Chirp, chirp!
# Finished making noise

machine = Machine.new 
machine.make_noise
# Powering up
# Buzzzzzz
# Shutting down 
```

Enter fullscreen mode Exit fullscreen mode

## 支持继承

在引入继承之前，这一切都很好。我们期望类从超类继承包装模块。我们来看看是不是这样。

```
module Flying
  def make_noise
    super
    puts "Is flying away"
  end
end

class Pigeon < Bird
  wrap Flying

  def make_noise
    puts "Coo!"
  end
end

pigeon = Pigeon.new
pigeon.make_noise
# Coo!
# Is flying away 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它并没有像预期的那样工作，因为`Pigeon`也在维护自己的包装模块集合。虽然为`Pigeon`定义的包装模块没有在`Bird`上定义是有道理的，但这并不是我们想要的。让我们想办法从整个继承链中获取所有包装器。

幸运的是，Ruby 提供了`Module#ancestors`方法来列出一个类(或模块)继承的所有类和模块。

```
Pigeon.ancestors # => [Pigeon, Bird, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

通过添加一个`grep`调用，我们可以挑选实际上用`Wrappable`扩展的调用。因为我们想首先用链中较高位置的包装器包装实例，所以我们调用`.reverse`来颠倒顺序。

```
Pigeon.ancestors.grep(Wrappable).reverse # => [Bird, Pigeon] 
```

Enter fullscreen mode Exit fullscreen mode

> ## 鲁比尼`#===`法
> 
> Ruby 的一些神奇之处可以归结为`#===`(或*大小写相等*)方法。默认情况下，它的行为就像`#==`(或 *equality* )方法一样。然而，有几个类覆盖了`#===`方法，以在`case`语句中提供不同的行为。这就是你如何在那些语句中使用正则表达式(`#===`相当于`#match?`)或者类(`#===`相当于`#kind_of?`)。像`Enumerable#grep`、`Enumerable#all?`或`Enumerable#any?`这样的方法也依赖于大小写相等方法。

现在，我们可以调用`flat_map(&:wrappers)`来获取一个列表，该列表包含继承链中定义为单个数组的所有包装器。

```
Pigeon.ancestors.grep(Wrappable).reverse.flat_map(&:wrappers) # => [Logging] 
```

Enter fullscreen mode Exit fullscreen mode

剩下的就是将它打包到一个`inherited_wrappers`模块中，并稍微修改新方法，以便它使用该方法而不是`wrappers`方法。

```
module Wrappable 
  def inherited_wrappers
    ancestors
      .grep(Wrappable)
      .reverse
      .flat_map(&:wrappers)
  end

  def new(*arguments, &block)
    instance = allocate
    inherited_wrappers.each { |mod|instance.singleton_class.include(mod) }
    instance.send(:initialize, *arguments, &block)
    instance
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后的试运行证实了一切都在按预期运行。包装模块只应用于它们所应用的类(及其子类)。

```
bird = Bird.new
bird.make_noise
# Started making noise
# Chirp, chirp!
# Finished making noise

machine = Machine.new
machine.make_noise
# Powering up
# Buzzzzz
# Shutting down

pigeon = Pigeon.new
pigeon.make_noise
# Started making noise
# Coo!
# Finished making noise
# Is flying away 
```

Enter fullscreen mode Exit fullscreen mode

## 就这么定了！

诚然，这些嘈杂的鸟是一个理论上的例子。但是可继承的类实例变量不仅仅是为了理解类是如何工作的。它们是 Ruby 中类只是对象的一个很好的例子。

我们承认，可继承的类实例变量在现实生活中可能非常有用。例如，考虑在一个模型上定义属性和关系，并且能够在以后自省它们。对我们来说，神奇的是玩这个游戏，更好地理解事物是如何工作的。打开你的思维去寻找下一个层次的解决方案。🧙🏼‍♀️

一如既往，我们期待听到您使用这种或类似模式构建的内容。有什么想法吗？请留言评论！。

*[贝内迪克特·德克](http://benediktdeicke.com)是 [Userlist.io](http://userlist.io) 的软件工程师兼 CTO。另外，他正在写一本关于用 Ruby on Rails 构建 [SaaS 应用程序的书。你可以通过](http://saasguidebook.com) [Twitter](http://twitter.com/benediktdeicke) 联系 Benedikt。*