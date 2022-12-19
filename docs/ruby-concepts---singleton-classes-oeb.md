# Ruby 概念——单例类

> 原文：<https://dev.to/rpalo/ruby-concepts---singleton-classes-oeb>

*封面图片致谢:[萨米尔·赛义德](https://www.sitepoint.com/author/ssaeed/)和 [SitePoint](https://www.sitepoint.com/javascript-design-patterns-singleton/) 。*

你有没有想过什么是“单例类”？你是否曾经和某人交谈或阅读一篇博客文章，并且“单例类”或“单例方法”被使用，而你只是微笑和点头，做一个心理笔记以便以后查找它？现在是你的时候了。现在是你的时刻了。我希望用更直观的语言来解释这个概念，并向您展示它有多方便。

旁注:很多信息来自于阅读[大卫·a·布莱克](http://www.davidablack.net/)的《扎实的 Rubyist 》。这本书包含了大量的信息，是目前我最喜欢的 Ruby 书籍之一。

旁注#2:至少，向下滚动到底部，寻找一些俗气而有益的基于单身的积极性。😁

## 代码

如果你已经写了很多 Ruby，你已经在不知不觉中使用了这些“单例类”!首先，我将向您展示您可能已经编写的代码，这样您就有了一些上下文。

```
class Config
  def self.from_file(filename)
    Config.new(YAML.load_file(filename))
  end
end

dev_config = Config.from_file("config.dev.yaml")
# => Config object with dev settings 
```

Enter fullscreen mode Exit fullscreen mode

你可能也见过这样的:

```
module Geometry
  class << self
    def rect_area(length, width)
      length * width
    end
  end
end

Geometry.rect_area(4, 5)
# => 20 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，您可能将这些称为“类方法”你基本上是对的。但是他们为什么工作呢？这里发生了什么事？

## 个性化

这是让 Ruby 如此出色的核心概念。单个的对象，即使是同一个类，也是互不相同的，它们可以定义不同的方法。在这个例子中，我将无耻地使用我们的宠物来帮助。

```
class Pet
  def smolder
    "Generic cute pet smolder"
  end
end

succulent = Pet.new
momo = Pet.new
willy = Pet.new

def momo.smolder
  "sassy cat smolder"
end

def willy.smolder
  "well-meaning dingus smolder"
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们在`succulent`上调用`smolder`时，我们没有改变，事情按计划进行。

```
succulent.smolder
# => Generic cute pet smolder" 
```

Enter fullscreen mode Exit fullscreen mode

[![Our succulent](../Images/3e940ee977b61252234978cd679187ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---cmx150_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/singleton-succulent.jpg)

但是当我们在`willy`或`momo`上调用`smolder`时，会发生一些不同的事情！

```
momo.smolder
# => "sassy cat smolder" 
```

Enter fullscreen mode Exit fullscreen mode

[![Momo is our cat](../Images/6d1ba5df27619912c94d2dc6f9777fbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--enR-nMr3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/singleton-momo.jpg)T3】

```
willy.smolder
# => "well-meaning dingus smolder" 
```

Enter fullscreen mode Exit fullscreen mode

[![Willy is our dog](../Images/9b8ae1ff8ae5f831a35d84bb028e4856.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GKX85EY8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/singleton-willy.jpg)

那么，这是如何工作的呢？我们是否为每只宠物重新定义了`smolder`？帮我一个忙，看看下面的输出:

```
succulent.singleton_methods
# => []
momo.singleton_methods
# => [:smolder]
willy.singleton_methods
# => [:smolder] 
```

Enter fullscreen mode Exit fullscreen mode

没错！你正在使用一个**单例方法**！现在，我想，我们已经准备好讨论什么是**单例类**。

## 什么是单例类？

首先，一个更一般的编程，不太特定于 Ruby 的问题:什么是 singleton？虽然对于不同的情况有不同的定义，但从本质上来说， **singleton** 就是只有一个的东西。它是同类中唯一的一个。

在 Ruby 的上下文中这意味着什么？这就是:当你用 Ruby 从一个类中实例化一个对象时，它知道它的类给它的方法。它还知道如何查找其类的所有祖先。这就是遗传起作用的原因。

> “哦，我的课没有那个方法？让我们检查一下它的父类。以及该类的父类。等等。”

Ruby 很酷的一点是祖先链的设计非常明确。对象搜索它们的祖先有一套特定的规则，因此调用哪个方法是毫无疑问的。

除了知道它的类之外，每个对象都用它知道的**单例类**创建。所有的单例类都是一种“幽灵类”,或者更简单地说，是一个包，用来保存任何只为这个特定对象定义的*方法。试试这个:* 

```
momo.singleton_class
# => #<Class:#<Pet:0x00007fea40060220>> 
```

Enter fullscreen mode Exit fullscreen mode

在继承层次结构中，它是不可见的，就在 objects 实际类的前面。但是，通过查看对象的祖先是看不到的。

```
momo.class.ancestors
# => [Pet, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们看看*单例类本身的祖先树* :

```
momo.singleton_class.ancestors
# => [#<Class:#<Pet:0x00007fea40060220>>, Pet, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到它在一开始就出现了。因此，当`momo`去寻找`smolder`方法时，它首先在它的单例类中寻找*。因为那里有一个`smolder`方法，它调用那个方法，而不是在树中进一步查找在`Pet`类中定义的方法。*

 *## 这和类方法有什么关系？

现在我们开始看到单例类的力量。别忘了每个类都只是类`Class`的一个对象。如果那句话让你开始换气过度，不要担心。我会解释的。

```
Pet.class
# => Class 
```

Enter fullscreen mode Exit fullscreen mode

而`Class`只是一个为你创建的每个实例(类)提供一些方法的类，就像其他任何类一样。

```
Class.instance_methods(false)
# => [:new, :allocate, :superclass] 
```

Enter fullscreen mode Exit fullscreen mode

所以，实际上，当你定义你计划在类上直接调用的“类方法”时，你实际上是在那个特定的类对象上定义方法——在它的单例类中！

```
class Pet
  def self.random
    %w{cat dog bird fish banana}.sample
  end
end

Pet.singleton_methods
# => [:random] 
```

Enter fullscreen mode Exit fullscreen mode

*和…* 如果单例类存在，它将成为从主类继承的类的 singleton_classes 的父类。举个例子应该会有帮助。

```
class Pet
  def self.random
    %w{cat dog bird fish banana}.sample
  end
end

class Reptile < Pet
  def self.types
    %w{lizard snake other}
  end
end

Reptile.singleton_methods
# => [:types, :random]
Reptile.singleton_class.ancestors
# => [#<Class:Reptile>, #<Class:Pet>, #<Class:Object>, #<Class:BasicObject>, Class, Module, Object, Kernel, BasicObject] 
```

Enter fullscreen mode Exit fullscreen mode

看看`Reptile`的 singleton 类是如何继承`Pet`的 singleton 类的，从而`Pet`可用的“类方法”也可以用于`Reptile`？

## 其他花絮

我认为到目前为止，我们已经涵盖了所有重要的部分。然而，有几个我认为很酷的有趣的细节有点不太相关:有点难以理解的`class << self`语法，创建类方法的不同方式，以及`extend`的使用。如果你感兴趣，请继续阅读。

### `Class << self`

使用`class`关键字实际上有两种方法:直接跟一个常量(a la `class Gelato`)，或者跟“铲运算符”和一个对象(a la `class << momo`)。你已经知道了第一个——这是你通常声明一个类的方式！让我们关注第二个，它是直接打开一个对象的单例类的语法。你可以把它想成本质上和我们上面做的定义方法一样。

我的意思是这样的:

```
# This:
def momo.snug
  "*snug*"
end

# is the same (pretty much) as this:
class << momo
  def snug
    "*snug*"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

当您重新打开常规类以添加更多功能时，您总是会这样做。

```
class Gelato
  attr_reader :solidity

  def initialize
    @solidity = 100
  end

  def melt
    @solidity -= 10
  end
end

# And re-open it to add one more method

class Gelato
  def refreeze
    @solidity = 100
  end
end

dessert = Gelato.new
5.times { dessert.melt }
dessert.solidity
# => 50
dessert.refreeze
# => 100 
```

Enter fullscreen mode Exit fullscreen mode

语法`class << object; end`只是重新打开对象的 singleton 类的另一种方式。这样做的好处是你可以一次定义多个常量和方法，而不是一次定义一个。

```
# Instead of:
def momo.pounce
  "pounce!"
end

def momo.hiss
  "HISS"
end

def momo.lives
  9
end

# We can do
class << momo
  def pounce
    "pounce!"
  end

  def hiss
    "HISS"
  end

  def lives
    9
  end
end

momo.singleton_methods
# => [:pounce, :hiss, :lives, :smolder] 
```

Enter fullscreen mode Exit fullscreen mode

当向一个类中添加多个类方法时，通常会看到以下内容:

```
class Pet
  class << self
    def random
      %w{cat dog bird fish banana}.sample
    end
  end
end

# Which, since "self" is inside of the class
# declaration, means that 'self == Pet', so you could
# also do this:

class Pet
  class << Pet
    def random
      # ...
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

也许你见过这种模式，但不知道它是什么，或者你知道它增加了类方法，但不知道为什么。现在你知道了！这都要感谢 singleton 类！

## `class << self` vs `def self.method` vs `def Pet.method`

有几种不同的方法来声明类方法。

```
# 1\. In global scope
def Pet.random
  %w{cat dog bird fish banana}.sample
end

# 2\. Inside the class definition, using 'self'
class Pet
  def self.random
    %w{cat dog bird fish banana}.sample
  end
end

# 3\. Inside the class definition, using the shovel
class Pet
  class << self
    def random
      %w{cat dog bird fish banana}.sample
    end
  end
end

# 4\. Outside the class definition, using the shovel
class << Pet
  def random
    %w{cat dog bird fish banana}.sample
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

那么有什么区别呢？？你什么时候使用一个或另一个？

好消息是它们基本上都是一样的。你可以使用任何一个让你最开心的和匹配你代码库风格的。唯一的区别是#3，以及它如何处理常量和范围。

```
MAX_PETS = 3

def Pet.outer_max_pets
  MAX_PETS
end

class Pet
  MAX_PETS = 1000

  class << self
    def inner_max_pets
      MAX_PETS
    end
  end
end

Pet.outer_max_pets
# => 3
Pet.inner_max_pets
# => 1000 
```

Enter fullscreen mode Exit fullscreen mode

看到`inner_max_pets`函数可以访问`Pet`类中的作用域和常量了吗？这是唯一的区别。放心大胆地继续使用您最喜欢的语法。

## 使用 Extend 安全地修改内置类

希望在某个时候，你已经读过一篇博文，或者有人警告过你重新打开内置 Ruby 类的危险。做类似下面的事情应该非常小心。

```
class String
  def verbify
    self + "ify"
  end
end

"banana".verbify
# => "bananaify" 
```

Enter fullscreen mode Exit fullscreen mode

危险包括意外地覆盖内置方法，使方法与同一项目中的其他库冲突，以及通常使事情不按预期进行。关键字`extend`可以帮助你做到这一切！

### 什么是扩展？

`extend`关键字与`include`非常相似，它允许你从其他类/模块中加载功能到你的类/模块中。然而，不同之处在于`extend`将这些方法放到了目标对象的 singleton 类中。

```
module Wigglable
  def wiggle
    "*shimmy*"
  end
end

willy.extend(Wiggleable)
willy.singleton_methods
# => [:wiggle, :smolder] 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果在类定义中使用`extend`而不是`include`，这些方法将作为类方法添加到类的单例类中，而不是作为实例方法添加到类本身中。

```
module Hissy
  def hiss
    "HISS"
  end
end

class Reptile
  extend Hissy
end

snek = Reptile.new
snek.hiss
# => Error!  Undefined method hiss for 'snek'
Reptile.hiss
# => "HISS" 
```

Enter fullscreen mode Exit fullscreen mode

### 这对我们有什么帮助？

所以，假设我们真的需要在我们使用的字符串上有那个`verbify`方法。虽然您可以创建并使用`String`的子类，但另一个选择是扩展单个字符串！

```
module Verby
  def verbify
    self + "ify"
  end
end

noun = "pup"
noun.extend(Verby)
noun.verbify
# => "pupify" 
```

Enter fullscreen mode Exit fullscreen mode

## 俗气的裹起来

所以请记住，singletons 不仅仅是一个听起来吓人但并不超级复杂的 Ruby 主题。你是真正的***单身者**——是的，你是一个人类，但是没有人和你一样。你有**类**和你自己的**做事方法**，这很有价值。现在，我们刚刚为您添加了更多的功能。* 

```
class << you
  def use_singletons_for_fun_and_profit
    # ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode**