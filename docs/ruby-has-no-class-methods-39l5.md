# Ruby 没有类方法

> 原文：<https://dev.to/edisonywh/ruby-has-no-class-methods-39l5>

## *等等…什么？*

[![Mind Blown.gif](img/34b8db6e6addfa98f0280ed9eb1ae688.png)T2】](https://i.giphy.com/media/xT0xeJpnrWC4XWblEk/giphy.gif)

我不知道你怎么想，但这是我第一次在`RubyConf KL 2018`听到`Nadia Odunayo`的演讲时的确切反应(请注意，这是我听过的最迷人的演讲之一！)，她在其中表明 *Ruby 没有类方法。*

当我刚开始学习 Ruby 时，我被告知 Ruby 基本上有两种类型的方法:

*   类方法
*   实例方法

```
class Foo
  def self.class_method
    puts "This is a class method of #{self}"
  end

  def an_instance_method
    puts "This is an instance method of #{self}"
  end
end

Foo.class_method #=> "This is a class method of Foo"
Foo.new.class_method #=> undefined method `class_method' for #<Foo:0x007f83960d2228> (NoMethodError)

Foo.an_instance_method #=> undefined method `an_instance_method' for Foo:Class (NoMethodError)
Foo.new.an_instance_method #=> This is an instance method of #<Foo:0x007f80aa92d150> 
```

Enter fullscreen mode Exit fullscreen mode

首先，这很有意义—

> 一个类是一个对象，当你在一个类上调用`.new`时，你创建了这个对象的一个新实例。
> 
> 因此，实例方法只在该类的实例上可用。

好吧，那么..当我说 Ruby 没有类方法时，我在说什么呢？

## 父母。爷爷奶奶。祖先..！

我们首先需要理解的是 Ruby 中的一个叫做**祖先链**的东西。

祖先链对于理解 Ruby 如何工作是至关重要的；整个方法调用链因为链而工作，但是这里我将尝试一个过于简化的解释。

在 Ruby 中，一切都继承`Object`，每个`Object`都继承自一个`BasicObject`。

让我们快速检查一下！

```
2.3.1 :001 > Foo.ancestors
 => [Foo, Object, Kernel, BasicObject]
2.3.1 :002 > Foo.superclass #superclass allow us to see what class the receiver inherited from. AKA, the parent of current class.
 => Object

2.3.1 :003 > Object.superclass
 => BasicObject

(Kernel is a Module, you could quickly verify it by doing Kernel.class) 
```

Enter fullscreen mode Exit fullscreen mode

当你调用一个方法时，它调用当前类上的方法；如果当前类没有该方法，它将沿着祖先链向上，直到找到一个。

还不清楚？我找到楚了！*暗示怪异的隐喻*

> 想象一下，你只是一个孩子，而那个试图智胜你的讨厌的小约翰尼不好意思地问你:“你知道我们太阳系里有什么吗？我打赌你不会！”。
> 
> 你实际上不知道答案，但你不会让小约翰尼获得他的胜利，所以你把寻找答案作为你的人生目标，你问你的父母:“我们太阳系里有哪些`.planets`？”。嗯，你的父母也不知道，所以*他们*继续问他们的父母，他们碰巧知道答案。
> 
> 现在轮到你对着小约翰尼不好意思地傻笑，并告诉他答案是什么。
> 
> 所以现在无论何时有人问你太阳系里有什么，你都会从你的祖父母那里得到答案。

这有什么关系？嗯，我们讨论了方法查找是如何工作的，在我的[上一篇文章](https://medium.com/fave-engineering/my-ruby-journey-hooking-things-up-91d757e1c59c)中，我谈到了用`include`、`extend`和`prepend`重用方法(`Module`)。现在让我们看看当你`include`、`extend`和`prepend`一个模块时它们各自的祖先链。

```
Awesome = Module.new

class IncludeModule
  include Awesome
end
IncludeModule.ancestors #=> [IncludeModule, Awesome, Object, Kernel, BasicObject]

class ExtendModule
  extend Awesome
end
ExtendModule #=> [ExtendModule, Object, Kernel, BasicObject]
# WHERE IS THE MODULE?!

class PrependModule
  prepend Awesome
end
PrependModule.ancestors #=> [Awesome, PrependModule, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

你会意识到一些奇怪的事情:

*   当你`include`时，模块被插入*在当前类的*之后，这意味着当你在当前类上找不到方法时，它将是你碰到的第一个东西。
*   `prepend`是`include`的反义词；它在前插入了*，所以从技术上讲，你仍然不能调用它。*
*   当你扩展一个模块时，你看不到祖先链上的模块

等等，我们知道当我们`extend`一个模块时，我们可以访问*类方法*，我们也知道方法调用是通过祖先链发生的，但是..如果模块不在祖先链上，`extend`如何工作？

原因是因为 Ruby 中有一个叫做 **Singleton Class** (有时也称为`Metaclass`、`Anonymous Class`或`Eigenclass`)的东西

每当在 Ruby 中初始化一个类时，都会生成两个对象——`Foo`本身和`Foo’s Singleton Class`。为了证明这一点，Nadia 在她的演讲中展示了一个使用`ObjectSpace`检查当前班级数量的好方法。`ObjectSpace`基本上是一种与内存中所有当前活动的 Ruby 对象进行交互的方式。

```
ObjectSpace.count_objects[:T_CLASS] #=> 920
Foo = Class.new
ObjectSpace.count_objects[:T_CLASS] #=> 922 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们理解`extend`在做什么之前，我们需要先理解`Singleton Class`，所以请耐心听我说，完成下一部分，然后我们就能理解`extend`的魔力了！

## 关于单例类的那个

`Singleton Class`就像它的别名一样，`Anonymous Class`是匿名的，因此不会出现在祖先链(！！！)但这并不意味着我们不能访问 singleton 类，事实上，我们可以使用一个叫做`.singleton_class`的简便方法。

```
class Foo
end

Foo.singleton_class #=> #<Class:Foo> 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看你刚刚创建的类的`.class`是什么。

```
class Foo
end

Foo.class #=> Class 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能已经注意到的，还有一种替代的方法来初始化一个类，如下:

```
Foo = Class.new 
```

Enter fullscreen mode Exit fullscreen mode

现在看起来真的很熟悉不是吗？Foo 是类的一个**实例。**

为什么这很重要？因为这意味着你认为正在`Foo`上执行的普通**类方法**实际上是`Class`上的一个实例方法。—它能工作是因为`Foo`是`Class`的一个实例！

我们现在知道单例类在祖先链上是不可见的。还记得链条上还有什么看不见的吗？该模块当你做一个`extend`！

我们可以检查单例类本身的祖先链。

```
HiddenModule = Module.new
class Foo
  extend HiddenModule
end

Foo.singleton_class.ancestors #=> [#<Class:Foo>, HiddenModule, #<Class:Object>, #<Class:BasicObject>, Class, Module, Object, Kernel, BasicObject]

# Well HiddenModule is not so hidden anymore! 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以证明我们所知道的`class_method`，实际上只是 singleton 类的一个实例方法。

```
module Mystery
  def resolved?
      true
  end
end

class Foo
  extend Mystery
end

Foo.resolved? #=> true
Foo.singleton_class.instance_methods.grep(/resolved?/) #=> [:resolved?]
Foo.method(:resolved?) #=> #<Method: Class(Mystery)#resolved?> 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们知道`extend`实际上在幕后做的是 singleton 类本身的一个`include`。来证明一下吧！

```
class Bar
  Bar.singleton_class.include Mystery
end

Bar.resolved? #=> true
Bar.singleton_class.instance_methods.grep(/resolved?/) #=> [:resolved?]
Bar.method(:resolved?) #=> #<Method: Class(Mystery)#resolved?> 
```

Enter fullscreen mode Exit fullscreen mode

*还管用！*

但是，是的，写完整的表格需要惊人的*三个*以上的单词，这是一个很大的工作量，所以我们有`extend`来节省我们的击键！

## 作者注

我会再发几篇文章，同时在`included`、`extended`和`prepended`钩子上查看我的[最后一篇](https://medium.com/fave-engineering/my-ruby-journey-hooking-things-up-91d757e1c59c)！

唷，这是我的第二篇博文。期待更多的学习和分享！