# 改变 Ruby 创建对象的方式

> 原文：<https://dev.to/appsignal/changing-the-way-ruby-creates-objects-2b8m>

Ruby 之所以伟大，是因为我们几乎可以根据自己的需要定制任何东西。这既有用又危险。搬起石头砸自己的脚很容易，但是如果小心使用，这可以产生非常有效的解决方案。

在 Ruby Magic，我们认为有用和危险是一个极好的组合。让我们看看 Ruby 如何创建和初始化对象，以及如何修改默认行为。

## 从类创建新对象的基础

首先，让我们看看如何在 Ruby 中创建对象。为了创建一个新的*对象*(或者*实例*)，我们在类上调用`new`。与其他语言不同，`new`不是语言本身的关键字，而是像其他语言一样被调用的方法。

```
class Dog
end

object = Dog.new 
```

Enter fullscreen mode Exit fullscreen mode

为了定制新创建的对象，可以向`new`方法传递参数。任何作为参数传递的东西，都将被传递给初始化器。

```
class Dog
  def initialize(name)
    @name = name
  end
end

object = Dog.new('Good boy') 
```

Enter fullscreen mode Exit fullscreen mode

同样，与其他语言不同，Ruby 中的初始化器也只是一个方法，而不是一些特殊的语法或关键字。

考虑到这一点，难道不应该像使用任何其他 Ruby 方法一样，使用这些方法吗？当然是啦！

## 修改单个对象的行为

假设我们希望确保某个特定类的所有对象总是打印日志语句，即使该方法在子类中被覆盖。一种方法是向对象的 singleton 类添加一个模块。

```
module Logging
  def make_noise
    puts "Started making noise"
    super
    puts "Finished making noise"
  end
end

class Bird
  def make_noise
    puts "Chirp, chirp!"
  end
end

object = Bird.new
object.singleton_class.include(Logging)
object.make_noise
# Started making noise
# Chirp, chirp!
# Finished making noise 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，使用`Bird.new`创建了一个`Bird`对象，并且使用其 singleton 类将`Logging`模块包含在结果对象中。

> 什么是单例类？
> Ruby 允许单一对象特有的方法。为了支持这一点，Ruby 在对象和它的实际类之间添加了一个匿名类。当调用方法时，在单例类上定义的方法优先于实际类中的方法。这些单例类对于每个对象都是唯一的，所以向它们添加方法不会影响实际类的任何其他对象。[在 Ruby 编程指南中了解关于类和对象的更多信息。](https://ruby-doc.com/docs/ProgrammingRuby/html/classes.html)

每当创建每个对象的 singleton 类时修改它有点麻烦。所以让我们把`Logging`类的包含移到初始化器中，为每个创建的对象添加它。

```
module Logging
  def make_noise
    puts "Started making noise"
    super
    puts "Finished making noise"
  end
end

class Bird
  def initialize
    singleton_class.include(Logging)
  end

  def make_noise
    puts "Chirp, chirp!"
  end
end

object = Bird.new
object.make_noise
# Started making noise
# Chirp, chirp!
# Finished making noise 
```

Enter fullscreen mode Exit fullscreen mode

虽然这样做很好，但是如果我们创建一个`Bird`的子类，比如`Duck`，它的初始化器需要调用`super`来保留`Logging`的行为。虽然有人可能会说，每当一个方法被覆盖时，恰当地调用`super`总是一个好主意，但是让我们试着找到一种不需要*调用*的方法。

如果我们不从子类中调用`super`，我们将失去`Logger`类的包含:

```
class Duck < Bird
  def initialize(name)
    @name = name
  end

  def make_noise
    puts "#{@name}: Quack, quack!"
  end
end

object = Duck.new('Felix')
object.make_noise
# Felix: Quack, quack! 
```

Enter fullscreen mode Exit fullscreen mode

相反，让我们覆盖`Bird.new`。如前所述，`new`只是一个在类上实现的方法。所以我们可以覆盖它，调用 super，并根据我们的需要修改新创建的对象。

```
class Bird
  def self.new(*arguments, &block)
    instance = super
    instance.singleton_class.include(Logging)
    instance
  end
end

object = Duck.new('Felix')
object.make_noise
# Started making noise
# Felix: Quack, quack!
# Finished making noise 
```

Enter fullscreen mode Exit fullscreen mode

但是，当我们在初始化式中调用`make_noise`时会发生什么呢？不幸的是，因为 singleton 类还没有包含`Logging`模块，所以我们不会得到想要的输出。

幸运的是，有一个解决方案:可以通过调用`allocate`从头开始创建默认的`.new`行为。

```
class Bird
  def self.new(*arguments, &block)
    instance = allocate
    instance.singleton_class.include(Logging)
    instance.send(:initialize, *arguments, &block)
    instance
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

调用`allocate`返回该类的一个新的、未初始化的对象。所以之后，我们可以包含附加的行为，然后，在那个对象上调用`initialize`方法。(因为默认情况下`initialize`是私有的，所以我们不得不求助于使用`send`。

> **关于`Class#allocate`**
> 的真相与其他方法不同的是，无法覆盖`allocate`。Ruby 没有在内部使用传统的方式为`allocate`分派方法。因此，只覆盖`allocate`而不覆盖`new`是行不通的。然而，如果我们直接调用`allocate`，Ruby 将调用重新定义的方法。[在 Ruby 的文档中了解更多关于`Class#new`和`Class#allocate`的信息。](https://ruby-doc.org/core-2.5.0/Class.html#method-i-allocate)

## 我们为什么要这样做？

和许多事情一样，修改 Ruby 从类创建对象的方式可能是危险的，事情可能会以意想不到的方式发生。

尽管如此，仍然有改变对象创建的有效用例。例如，当从数据库创建对象而不是构建未保存的对象时，ActiveRecord 使用不同的`init_from_db`方法来改变初始化过程。它还使用`allocate`用`becomes`在不同的[单表继承](https://en.wikipedia.org/wiki/Single_Table_Inheritance)类型之间转换记录。

最重要的是，通过体验对象创建，您可以更深入地了解它在 Ruby 中是如何工作的，并且可以接受不同的解决方案。我们希望你喜欢这篇文章。

我们很想知道你通过改变 Ruby 创建对象的默认方式实现了什么。请不要犹豫留下评论。

*[贝内迪克特·德克](http://benediktdeicke.com)是 [Userlist.io](http://userlist.io) 的软件工程师兼 CTO。另外，他正在写一本关于用 Ruby on Rails 构建 [SaaS 应用程序的书。你可以通过](http://saasguidebook.com) [Twitter](http://twitter.com/benediktdeicke) 联系 Benedikt。*