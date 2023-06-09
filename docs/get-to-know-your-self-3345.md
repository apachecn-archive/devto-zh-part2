# 了解你自己(重构)

> 原文：<https://dev.to/meg_gutshall/get-to-know-your-self-3345>

虽然 Ruby 是一种非常用户友好的语言，但它也有一些难题——特别是:`self`。`self`是一个 Ruby 保留字，或者是一个在 Ruby 语言中专门设计为具有特殊含义的字。`self`的作用域(或程序可见性)是任何类或类的实例，我们也可以称之为`object`。这意味着`self`可以让开发者根据上下文引用特定的类或类的实例——这取决于他们程序的需求——而不需要使用特定的变量名。

如果方法是一个消息，谁是接收者？对象！很多时候，对象和`self`是一体的，但有时它们不是。在下面的例子中，我将解释`self`如何根据正在发送的“消息”而变化。

```
class AfroPet
  attr_accessor :species
  @@all = []

  def initialize(species)
    @species = species
    @@all << self
  end

  def self.all
    @@all
  end
end

hipmo = AfroPet.new("Hippopotamouse")
eleham = AfroPet.new("Eleham")
ghound = AfroPet.new("Giraffet Hound") 
```

上面的代码显示了有一个`species`属性和一个`@@all`类变量的`AfroPet`类。两个方法被定义为`AfroPet`类的一部分:`#initialize`和`self.all`。此外，还创造了三种新的`AfroPet`物种，包括口袋大小的 Eleham！

[![Elephant shrew in the Berlin Zoo](img/3cdfd515c4a261722c44c0bb3ebdbc10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D-vxtpvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x36hlu3cc8gisa698wnk.jpg)

## `self`泛指一个类的实例

我们首先看到在第 7 行的`#initialize`方法中，`self`被铲入`@@all`类变量。节目中的这一刻，`self`指的是什么？

请记住，每当我们实例化一个新对象(也称为创建一个类的新实例)时，我们在类本身上调用`#new`，然后立即在刚刚创建的对象*上调用`#initialize`——就像一击两击。通过将`@@all << self`写入我们的`#initialize`方法，我们使得新的实例能够在创建时自动保存到`@@all`类变量中，而不是必须在以后手动添加，这节省了我们的时间并保持我们的代码干燥！*

回到我的消息类比，在这个例子中，`#new`是发送者，而`#initialize`方法是消息。此外，因为我们发现`self`在的`#initialize`方法中被利用*，它被认为是所谓的方法范围的一部分，确认了`self`确实引用了新创建的对象。*

## `self`泛指一个阶层

接下来我们在第 10 行的`self.all`类方法中看到了`self`。这是一个类方法，旨在返回`@@all`类变量的值，该变量是`AfroPet`类的实例数组。正如您在上面看到的，我们创建了`AfroPet`类的三个新实例:`hipmo`、`eleham`和`ghound`。那么当我们调用`self.all`类方法时会发生什么呢？

```
AfroPet.all
#=> [#<AfroPet:0x000055b7db32c9c0 @species="Hippopotamouse">,
     #<AfroPet:0x000055b7db32c970 @species="Eleham">,
     #<AfroPet:0x000055b7db32c920 @species="Giraffet Hound">] 
```

在我们的终端中键入`AfroPet.all`之后，它返回一个数组，包含我们之前实例化的三个`AfroPet`对象，表明程序认识到在这种情况下，`self`引用的是`AfroPet`类。我们之所以知道这一点，是因为这个特定“消息”(或任何在方法名前带有`self.`前缀的类方法)的接收者是类本身，这意味着`self`是 ***而不是*** 作用于任何特定实例，而是作用于*类*。

那么`self`如何知道何时改变它所引用的对象呢？两种方式:

1.  第一个是我们之前讨论过的上下文。
2.  第二个是这个保留字的内置规则: ***在程序中的任何给定时间都有且只有一个`self`。*** 永远不会出现`self`一次引用一个以上对象的情况，因此让上下文来决定`self`在程序中任何给定点的实际含义。

我知道，这是一大堆信息，你现在可能已经晕头转向了，但是让我们再做一个`self`的例子。我会带你走一遍！

```
class Creamery
  attr_accessor :creamery_name, :ice_cream_flavor, :creamery_flavors
  @@all = []

  def initialize(creamery_name, ice_cream_flavor)
    @creamery_name = creamery_name
    @ice_cream_flavor = ice_cream_flavor
    @creamery_flavors = []
    self.creamery_flavors << @ice_cream_flavor
    @@all << self
  end

  def new_flavor(ice_cream_flavor)
    @ice_cream_flavor = ice_cream_flavor
    self.creamery_flavors << @ice_cream_flavor
  end

  def self.all_creamery_flavors
    @@all.each do |creamery|
      creamery.creamery_flavors.each do |flavor|
        puts "#{creamery.creamery_name} " + flavor
      end
    end
  end
end

bnj = Creamery.new("Ben and Jerry's", "Chunky Monkey")
hersheys = Creamery.new("Hershey's", "Chocolate")
nelsons = Creamery.new("Nelson's", "Graham Slam") 
```

在定义了`Creamery`类之后，我们创建了三个新的 creameries: `bnj`、`hersheys`和`nelsons`。

在这个例子中，`#initialize`方法创建了一个新的 creamery，给出了`creamery_name`和`ice_cream_flavor`属性。在这里，我们还声明了一个名为`@creamery_flavors`的空数组来存储这个奶油厂的冰淇淋口味。将`ice_cream_flavor`添加到 creamery 的`@creamery_flavors`实例变量中，并将 creamery 本身添加到`@@all`类变量中。*记住:在上一个例子中，我们看到了在`#initialize`方法中使用时`self`是如何引用新创建的对象的。*

您可以通过调用 creamery 实例上的`#new_flavor`并将新的`ice_cream_flavor`声明为该方法的字符串参数来为 creamery 添加新的味道。新的味道然后被添加到奶油厂的`@creamery_flavors`阵列。这里使用`self`的上下文看起来熟悉吗？(提示:见`#initialize`法！)

```
hersheys.new_flavor("Vanilla")
#=> ["Chocolate", "Vanilla"]
hersheys.new_flavor("Strawberry")
#=> ["Chocolate", "Vanilla", "Strawberry"]

bnj.new_flavor("Chubby Hubby")
#=> ["Chunky Monkey", "Chubby Hubby"]
bnj.new_flavor("Oat of this Swirled")
#=> ["Chunky Monkey", "Chubby Hubby", "Oat of this Swirled"] 
```

最后，`self.all_creamery_flavors`类方法迭代`@@all`类变量中的 creameries，并显示包含在`Creamery`类中的所有冰淇淋口味。它是这样工作的:

1.  迭代`@@all`类变量以访问每个 creamery 实例。
2.  对于每个 creamery，调用`#creamery_flavors`来访问它们的`ice_cream_flavor`属性的实例变量数组。
3.  在其上链接`#each`方法，再次迭代这个嵌套数组，这一次`puts`将味道输出到终端。

最初的`@@all`类变量将在该方法执行结束时自动`return`。

```
Creamery.all_creamery_flavors

"Ben and Jerry's Chunky Monkey"
"Ben and Jerry's Oat of this Swirled"
"Ben and Jerry's Chubby Hubby"
"Hershey's Chocolate"
"Hershey's Vanilla"
"Hershey's Strawberry"
"Nelson's Graham Slam"

#=> [#<Creamery:0x00005558bceb7e08 @creamery_name="Ben and Jerry's", @ice_cream_flavor="Oat of this Swirled", @creamery_flavors=["Chunky Monkey", "Chubby Hubby", "Oat of this Swirled"]>,
     #<Creamery:0x00005558bceb7d68 @creamery_name="Hershey's", @ice_cream_flavor="Strawberry", @creamery_flavors=["Chocolate", "Vanilla", "Strawberry"]>,
     #<Creamery:0x00005558bceb7cc8 @creamery_name="Nelson's", @ice_cream_flavor="Graham Slam", @creamery_flavors=["Graham Slam"]>] 
```

你刚学了`self`！如果你觉得你已经很好地掌握了`self`的概念，那就用一勺你最喜欢的冰淇淋来庆祝吧！如果没有，请查看以下有用的资源:

[红宝石`self`](https://learn.co/lessons/ruby-self-readme)
[红宝石中的自我:全面概述](https://airbrake.io/blog/ruby/self-ruby-overview)
[了解`self`红宝石中的](https://www.honeybadger.io/blog/ruby-self-cheat-sheet/)

...然后吃一勺冰淇淋。:D

### 重构奖金部分

在我最初写这篇博文一年多后，我有机会回来回顾我以前的博文。*我的天*这人是 ***不是*** 漂亮！我试着清理一下，用更好的例子/更好地解释。如果你有任何意见要补充或看到明显错误的地方，请在推特上发短信给我，邮箱是 [@meg_gutshall](https://twitter.com/meg_gutshall) 或发邮件给我，邮箱是[meghan.gutshall@gmail.com](mailto:meghan.gutshall@gmail.com)。

在我的第二轮研究中，我在 Ruby 的`self`上发现了一个非常有趣且高水平的帖子，并希望将其作为额外内容分享。**警告:**这是令人困惑的狗屎，我还远远没有感觉完全舒服。如果你是一个初学者，我强烈建议你坚持上面的三个链接，并把这个链接收藏起来，以便以后再次访问:[Ruby 中的元编程:这都是关于自我的](https://yehudakatz.com/2009/11/15/metaprogramming-in-ruby-its-all-about-the-self/)。

此外，我为这篇重构后的博客文章重写了上面使用的两个代码示例，并希望为第二个示例展示一种可能具有普遍用途的方法——它只是不一定与`self`相关。

```
class Creamery

  ...

  def all_flavors
    @creamery_flavors.each {|flavor| puts flavor}
  end

  def self.all_creamery_flavors
    @@all.each {|creamery| creamery.all_flavors}
  end
end 
```

您可能已经注意到，我们不得不在博文的`self.all_creamery_flavors`方法中迭代两次。在这里，我创建了一个`#all_flavors`实例方法，它迭代`@creamery_flavors`数组，`puts`就像博客文章中的例子一样，处理每一种味道。唯一的区别是，这个方法的范围是一个单独的奶精厂。然后，我重构了`self.all_creamery_flavors`类方法，以便在新的`#all_flavors`实例方法通过第一次迭代访问单个 creamery 实例时调用它。