# 在 Python 中协调数据类和属性

> 原文：<https://dev.to/florimondmanca/reconciling-dataclasses-and-properties-in-python-4ogc>

<small>*原载于 2018 年 10 月 6 日 blog.florimondmanca.com。*</small>

*如果等不及 TL；博士，跳到本文末尾的[经验教训](#lessons-learned)。*

我一直在玩 Python 3.7 的[数据类](https://docs.python.org/3/library/dataclasses.html#dataclasses.dataclass)——到目前为止，它们超级棒。在很大程度上，它们非常直观，易于使用和定制。

然而，有一件事我一直在纠结，却找不到在线帮助，那就是**在 dataclasses** 上实现 Python 属性。很有可能——我并不孤单。

所以我才决定一劳永逸的解决问题。我想弄清楚如何协调数据类和属性。

因此，我在电脑前坐下，启动了一个翻译程序，写下了我寻找解决方案的过程。经过一夜的反复试验和一些措辞上的改进，结果就是这篇博文！

作为一个令人愉快的副作用，您将在中看到 dataclasses 的许多功能。我将从快速概述数据类开始，然后我们将讨论代码。

当然，你会在 [GitHub](https://github.com/florimondmanca/dataclasses-properties) 上找到支持这篇文章的所有代码。

## 数据类:一万英尺的概述

简单地说，数据类是用来保存数据的**类。他们在 [PEP-557](https://www.python.org/dev/peps/pep-0557/#rationale) 中的规范是基于这样一个事实:我们编写的许多类仅仅被用作可编辑的数据容器。当这种情况发生时，我们会花时间编写样板代码，这通常会导致一个丑陋的`__init__()`方法，它有大量的参数和同样多的行来存储它们作为属性——更不用说处理[默认参数](https://dev.to/florimondmanca/python-mutable-defaults-are-the-source-of-all-evil-6kk) …**

<figure>

[![](img/e9d48b066cfb4a92a0009010f82917e9.png)T2】](https://i.giphy.com/media/3scoQYue48MeZL2bBi/giphy.gif)

<figcaption>Dataclasses, featuring Raymond Hettinger in a GIF.</figcaption>

</figure>

而且有！答案是: **dataclasses** 。🎉

Python 在名副其实的[数据类](https://docs.python.org/3/library/dataclasses.html)模块中实现了数据类，其超级明星是`@dataclass`装饰器。这个装饰器实际上只是一个**代码生成器**。它利用 Python 的类型注释(如果你仍然不使用它们，[你真的应该](https://dev.to/florimondmanca/why-i-started-using-python-type-annotations--and-why-you-should-too-2a3m))来**自动生成样板代码**否则你必须自己机械地键入。

作为比较，下面是如何使用常规的类声明
创建一个带有`wheels`属性的`Vehicle`类

```
class Vehicle:
    def __init__(self, wheels: int):
        self.wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的，真的。现在，`@dataclass`版本:

```
# 0_initial.py from dataclasses import dataclass

@dataclass
class Vehicle:
    wheels: int 
```

Enter fullscreen mode Exit fullscreen mode

信不信由你——这两个代码片段是严格等价的！这实际上是一个胜利，因为除了`__init__()`，`@dataclass`免费产生了一堆额外的东西，包括一个英俊的`__repr__()` :

```
>>> car = Vehicle(wheels=4)
>>> car
Vehicle(wheels=4) 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，dataclasses 是一种简单、优雅、Pythonic 式的创建保存数据的类的方法。🐍

但是…

## 问题

当获取/设置属性时，我有时求助于`@property`装饰器来实现特定的逻辑。这就是 Pythonic 实现 getters 和 setters 的方式。

基于前面的`Vehicle`类，我将把`wheels`属性设为私有，并在它上面放一个`@property`:

```
class Vehicle:

    def __init__(self, wheels: int):
        self._wheels = wheels
        # note the underscore — it's now private! 👻 
    @property
    def wheels(self) -> int:
        print('getting wheels')
        return self._wheels

    @wheels.setter
    def wheels(self, wheels: int):
        print('setting wheels to', wheels)
        self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

下面是它的样子:

```
>>> car = Vehicle(wheels=4)
>>> car.wheels = 3
setting wheels to 3
>>> car.wheels
getting wheels
3
>>> 
```

Enter fullscreen mode Exit fullscreen mode

现在的问题是，如何在数据类上实现这样的属性？

## 等等，那么*这个*是问题吗？

你可能认为这是一个微不足道的问题。请注意，**这可不是小事**。

数据类为您生成了`__init__()`方法——这很好。他们甚至提供了一个`__post_init__()`钩子方法，以防你想做更多的初始化工作(参见[后初始化处理](https://docs.python.org/3/library/dataclasses.html#post-init-processing))。

然而，这意味着你不能像普通类一样做同样的事情，也就是说，把一个公共的参数(例如`wheels`)存储到一个私有的属性(`_wheels`)中，这样你就可以构建一个`@property`。

这就是问题的来源。老实说，这让我有点头疼。

因为我认为这个问题的解决很有趣，所以我将带您连续尝试 5 次，以正确地在`Vehicle`类的 dataclass 版本上实现该属性。

## 尝试 1:声明私有字段

首先，让我们保持简单。我们希望将`wheels`存储在私有字段中，并在`@property`中使用它，对吗？那么为什么不简单地在数据类上声明一个`_wheels`字段呢？

```
# 1_private_field.py from dataclasses import dataclass

@dataclass
class Vehicle:
    _wheels: int

    # wheels @property as before 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这不起作用——否则这篇博文就没什么用了！😙

原因是因为构造函数现在需要一个`_wheels`参数，而不是`wheels`。

```
>>> car = Vehicle(wheels=4)
Traceback (most recent call last)
<ipython-input-3-9c9de8fb1422> in <module>()
----> 1 car = Vehicle(wheels=4)
TypeError: __init__() got an unexpected keyword argument 'wheels' 
```

Enter fullscreen mode Exit fullscreen mode

公平地说，这只是在做它的工作。然而，这不是我们想要的。

## 尝试二:利用`InitVar`

如果您通读文档，您将了解到`InitVar`允许您实现[仅初始化变量](https://docs.python.org/3/library/dataclasses.html#init-only-variables)。这些变量可以传递给构造函数，但不会存储在类的属性中。相反，变量作为参数传递给`__post_init__()`。

为什么不用它来创建一个 init 专用的`wheels`变量，并将它存储在一个`_wheels`字段中呢？我们只需要给后者一个缺省值(例如`None`，这样它就不是构造函数所需要的:

```
# 2_initvar.py from dataclasses import dataclass, InitVar

@dataclass
class Vehicle:

    wheels: InitVar[int]
    _wheels: int = None  # default given => not required in __init__() 
    def __post_init__(self, wheels: int):
        self._wheels = wheels

    # wheels @property as before 
```

Enter fullscreen mode Exit fullscreen mode

当然，`__init__()`现在需要一个`wheels`参数而不是`_wheels`，这正是我们想要的。

然而，`@dataclass`现在使用`_wheels`生成其他样板代码和魔术方法，这是有问题的。

```
>>> car = Vehicle(wheels=4)
setting wheels to 4
>>> car
Vehicle(_wheels=4)  # 😕 
```

Enter fullscreen mode Exit fullscreen mode

## 尝试三:利用`field()`

深入研究文档，我发现可以使用 [field()](https://docs.python.org/3/library/dataclasses.html#dataclasses.field) 函数微调字段生成行为。您可以向它传递一个`default`值，它接受一个`repr`参数来控制该字段是否应该包含在生成的`__repr__()`中。下面是它在`_wheels` :
上使用时的样子

```
# 3_field.py from dataclasses import dataclass, field

@dataclass
class Vehicle:

    wheels: InitVar[int]
    _wheels: int = field(default=None, repr=False)

    # __post_init__() as before
    # wheels @property as before 
```

Enter fullscreen mode Exit fullscreen mode

很好——我们不再将`_wheels`包含在`__repr__()`中。但是我们也没有`wheels`！

```
>>> car = Vehicle(wheels=4)
setting wheels to 4
>>> car
Vehicle()  # Where is `wheels=4`? 😕😕😕 
```

Enter fullscreen mode Exit fullscreen mode

## 尝试四:将`wheels`做成合适的字段

在之前的尝试中，`wheels`是一个`InitVar` —而不是一个字段。这一次，让我们将它声明为一个独立的字段。在构造函数中传递它是可能的，这一次它应该包含在`__repr__()`中。

好的一面是，稍后在类中声明的`wheels`的`@property`定义不会干扰`@dataclass`的生成过程— *，因为它不是类型注释*，而这正是`@dataclass`生成字段所依赖的。

这可能开始有点复杂，所以让我给你看一些代码。这次我将为`wheels`完整再现`@property`:

```
# 4_wheels_field.py from dataclasses import dataclass, field

@dataclass
class Vehicle:

    wheels: int  # Now a regular dataclass field 
    # The rest just as before: 
    _wheels: int = field(default=None, repr=False)

    def __post_init__(self):
        # Note: wheels is not passed as an argument
        # here anymore, because it is not an
        # `InitVar` anymore.
        self._wheels = self.wheels  # (1) 
    @property
    def wheels(self) -> int:
        print('getting wheels')
        return self._wheels

    @wheels.setter
    def wheels(self, wheels: int):
        print('setting wheels to', wheels)
        self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们正在到达那里，不是吗？

不幸的是，不尽然。😞这个实现中有一个问题。

实际上，您可能认为第`(1)`行将赋予构造函数的`wheels`的值(存储在`self.wheels`中)放入`_wheels`。例如，调用`Vehicle(wheels=4)`会得到`_wheels == 4`。可悲的是，事实并非如此！

原因如下:执行`__post_init__()`时，`self.wheels`是`wheels`属性的 getter 返回的值— *而不是*在`__init__()`期间最初存储的值！而那个 getter 返回`self._wheels`，默认是`None`。

我知道，现在一切都乱了套，但请忍耐一下

```
>>> car = Vehicle(wheels=4)
setting wheels to 4
getting wheels  # hint: this is (1) being executed >>> print(car.wheels)
getting wheels
None  # nope, nothing in there… 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细想想，我们在(1)中所做的只是用它自己的值替换`_wheels`。如果你问我，我会说这毫无用处。如果我们甚至没有实现`__post_init__()`，我们实际上会得到相同的结果。

咄！那么我们能做什么呢？😩

还好有希望！

## 尝试 5:从构造函数中排除`_wheels`

让我警告你——这第五次也是最后一次尝试会成功，我马上会解释为什么会成功的原因。

在这一点上，你感到悲伤是对的——我自己也感到悲伤。但是不要害怕！文档中有一样东西我们还没有尝试过。

到目前为止，已经使用`field(default=None, repr=False)`声明了`_wheels`属性。在这里使用`default=None`意味着我们能够省略在构造函数中为`_wheels`传递一个值——在`__init__()`期间它将被赋予`None`的值。但是，仍然可以在构造函数中给它一个值，一切都会按预期进行:

```
>>> car = Vehicle(wheels=4, _wheels=3)
setting wheels to 4
getting wheels
>>> car.wheels
getting wheels
3 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们想办法从构造函数中移除`_wheels`参数怎么样？这能解决我们的问题吗？(*剧透预警:会的。*

您猜怎么着:`field()`接受了一个`init`参数来达到这个目的。`field()`上的文件写道:

> `init`:如果为真(默认)，该字段作为参数包含在生成的`__init__`方法中。

听起来微不足道，对吗？好吧，让我们试着在`_wheels`上使用它(我去掉了`__post_init__()`钩子，因为我们之前已经表明它实际上是没用的):

```
# 5_init_false.py from dataclasses import dataclass, field

@dataclass
class Vehicle:

    wheels: int
    _wheels: int = field(init=False, repr=False)

    @property
    def wheels(self) -> int:
        print('getting wheels')
        return self._wheels

    @wheels.setter
    def wheels(self, wheels: int):
        print('setting wheels to', wheels)
        self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

你猜怎么着？这刚刚解决了我们所有的问题。

因为我们用了`init=False`，所以`@dataclass`生成的构造函数根本不会初始化`_wheels`。

**然而**，它*会*用传递给构造函数的值初始化`wheels`。如果我们能够提取生成的代码，那么`__init__()`中的一条指令应该是这样的:

```
self.wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

现在，你告诉我——这到底是怎么回事？

是的，没错。它将执行 setter！🙀

看啊！实际上，从第 4 次尝试开始，我们就在 setter 中看到了 print 语句！

```
>>> car = Vehicle(wheels=4)
setting wheels to 4  # the `wheels` setter being called 
```

Enter fullscreen mode Exit fullscreen mode

让我提醒你一下那个 setter 的代码:

```
@wheels.setter
def wheels(self, wheels: int):
    print('setting wheels to', wheels)
    self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

它设置了`_wheels`的值！

因此，在构造函数中传递的`wheels`的值被放入`_wheels`中，而不是其他地方，因为在生成类之后，`wheels`只引用`@property`，而不是 dataclass 上的字段。

仔细想想，这正是我们在好的常规类上实现属性时所做的事情。记得吗？

```
class Vehicle:

    def __init__(self, wheels: int):
        self._wheels = wheels
        # This is equivalent to calling:
        # `self.wheels = wheels`
        # which *is* what the __init__() method
        # now generated by @dataclass actually does. 
```

Enter fullscreen mode Exit fullscreen mode

警告:**这种方法只适用于属性的 setter 被实现的情况**。如果我们只实现了一个 getter(即创建一个只读字段)，`__init__()`方法将不能分配属性，并且会崩溃。这是有意的行为，因为**数据类被设计成可编辑的数据容器**。如果你真的需要只读字段，你首先就不应该求助于数据类。也许`NamedTuple` s 是一个可行的选择——它们是数据类的只读等价物。

总之，长话短说…

## 成功！

我们已经成功地实现了数据类的属性。🎉

<figure>

[![](img/d2cf0e3f7d2a15008aee4f36a7c5df44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iwzxCcjO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2jhkbw.jpg)

<figcaption>Memes will never let you down.</figcaption>

</figure>

老实说，**出乎意料的不容易**。我们已经进行了五次不同的尝试，浏览了文档，并煞费苦心地编写了通向数据类属性的代码。

那么在所有这些争论之后，我们是否可以至少推导出一个在数据类上实现属性的快速方法？

答案是:是的，我们可以。✌️

## 吸取教训

您是否注意到了在常规类和数据类上使用`@property`的模式？

看，这里是常规类版本:

```
class Vehicle:

    def __init__(self, wheels: int):
        self._wheels = wheels

    @property
    def wheels(self) -> int:
        return self._wheels

    @wheels.setter
    def wheels(self, wheels: int):
        self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

和 dataclass 版本，使用 diff 语法来突出差异:

```
+ from dataclasses import dataclass, field 
+ @dataclass
  class Vehicle:

+     wheels: int
+     _wheels: field(init=False, repr=False) 
-     def __init__(self, wheels: int):
-         self._wheels = wheels 
      @property
      def wheels(self) -> int:
          return self._wheels

      @wheels.setter
      def wheels(self, wheels: int):
          self._wheels = wheels 
```

Enter fullscreen mode Exit fullscreen mode

用简单的语言写给你们这些文学怪胎:

> **如何在数据类上实现属性:**
> 
> 1.  移除`__init__()`
> 2.  将属性声明为字段
> 3.  使用`field(init=False, repr=False)`添加相关的私有变量。

## 冤家路窄！

如果你设法读到这里，恭喜你！这篇博客文章涉及了一个非常具体和技术性的话题，但是我在撰写这篇文章和弄清楚如何实现数据类属性时获得了很多乐趣。

对于那些想知道——阿帕奇卡夫卡系列仍在继续！我觉得小小的休息一下不会有什么坏处，我觉得这给了我一个机会去写一些自发的东西。

我希望你喜欢这篇文章，正如在简介中提到的，你可以在 [GitHub](https://github.com/florimondmanca/dataclasses-properties) 上找到所有代码。下次见！💻

## 保持联系！

如果你喜欢这篇文章，你可以在 Twitter 上找到我，获取更新、公告和新闻。🐤