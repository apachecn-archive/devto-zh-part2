# 不那么糟糕的模块名

> 原文：<https://dev.to/epigene/less-awful-module-names-1m56>

我喜欢正确命名事物。我讨厌设计。
我就是没办法让自己去读可枚举源。

这是为什么呢？！

#### TL；速度三角形定位法(dead reckoning)

停止根据模块对包含类的作用来命名模块。相反，根据他们提供的**行为**来命名他们。

```
# bad
module Enumerable
module Awarable
module DatabaseAuthenticatable 
include Enumerable

# good
module Enumeration
module Awareness
module DatabaseAuthenticating 
include Enumeration 
```

Enter fullscreen mode Exit fullscreen mode

### 充分讨论

很长一段时间我都觉得我就是这样，你不可能喜欢你用的每一颗宝石，对吗？

然后，有一天，我在我们的代码库中遇到了一个名为`Insurable`的模块，我说:“那只是****愚蠢！模块可保到底意味着什么？！好吧，可能是模块让**包括**对象*可保*？这听起来很像是用`Registerable`和`Database Authenticatable`设计的，恶心！”。所以我对这种命名“惯例”有一种本能的嘲笑，而且根据我自己的经验，我更深一层。在模块名后面加上“able”让我毛骨悚然，这是怎么回事？它可能根据模块的包含对接收类的影响来定义模块，而根据模块本身来定义模块可能更好。也许这个名字透露出一种代码味道，一种耦合，一种对包含对象中功能的依赖。

#### 耦合是个问题吗？

我对一些耦合没意见。当然，对于`Enumerable`,耦合是可以理解的，它依赖于已经定义的`#each`,这是可以的。模块应该通过提升方法来反映这种期望。通常是这样的:

```
module Enumerable
  def each
    raise("Expected to be overridden")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

请阅读 Ilija Eftimov 精彩的[隔离测试 mixin](https://semaphoreci.com/community/tutorials/testing-mixins-in-isolation-with-minitest-and-rspec)来了解如何对此进行规范。

#### 会不会是听起来的样子？

事实证明，让我对“能干”感兴趣的是，谈论它很尴尬。考虑:

> —这是可枚举模块。
> — Wut，模块是可枚举的？
> —不，不，模块使包含类可枚举，看到了吗？
> —所以模块允许枚举？
> —是的。
> —提供枚举？
> —嗯嗯..

甚至不要让我开始像“意识”这样的分词。肯定不是`module Awarable`，而是`module Awareness (behavior/property)`。

我建议你根据模块封装的行为来命名模块，尽量直接表达出来，不要通过包含类。不要害怕复杂的词汇，看看思想的同义词。