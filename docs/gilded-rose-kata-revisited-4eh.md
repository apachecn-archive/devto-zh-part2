# 重温镀金玫瑰形

> 原文：<https://dev.to/sandordargo/gilded-rose-kata-revisited-4eh>

如果你对编写道场代码和解形感兴趣，你可能已经尝试过由[艾米丽·巴切](https://twitter.com/emilybache?lang=en)创作的[镀金玫瑰形](https://github.com/emilybache/GildedRose-Refactoring-Kata)。

在这个表格中，您将获得一些现有的代码，用于处理商店中产品的质量和过期前的天数属性。代码在一个巨大的函数中处理几乎所有的事情。毫不奇怪，形的目标是重构代码。此外，还有一个新的功能要实现。

我以前做过几次这个形，但是最近当我和我的团队再次做的时候，我们讨论了一个完全不同的方法，我想分享它的一些方面。

但首先要做的是。我之前是怎么做到的？

让我们从测试方面开始。

要么我只是自动化了[特性测试](https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/master/cpp/GildedRoseTextTests.cc)的执行和评估，要么我实现了单元测试。在后一种情况下，我一丝不苟地阅读了[需求](https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/master/README.md)，并且一个接一个地添加了单元测试。如果我在实现中发现了一个 bug，我会根据与我的合作伙伴的讨论来修复它或记录它。在我看来，在这种情况下你应该做什么并不明显。可能错误的行为是可以接受的，因为可能你的客户接受了错误的输出，如果你修复了你发现的错误，你实际上打断了他们的流程。这种情况在现实生活中也发生在我们身上，尤其是当我们维护长寿产品的时候。

我采用的测试方法会对我重构代码的方式产生影响。当我只使用特性测试时，通常我会使用 IDE 的功能来进行重构。我提取直到我放弃，我尽可能地重新命名。一旦代码可读性更好了，我也开始做一些手工重构。

如果我一个接一个地实现单元测试，我可能会更大胆地重构/重新实现小块的功能。从一开始。

代码的结构可能很大程度上取决于语言/IDE 组合的选择。例如，使用 C++和 Eclipse，您不能将一些代码提取到一个新的类中，但是您可以使用 Java 和 IntelliJ(也许 Java 和 Eclipse 也是这样)。换句话说，不用想太多，用 Java 比用 C++更容易得到一个更面向对象的代码。(这是好事吗？我把那留给你。)

在这种情况下，为了节省时间，我们决定只进行特性测试。我们的主要目标是尝试通过抽象进行分支。

该模型背后的主要思想是在每一小步之后都有一个可部署的代码版本，该版本可以是重构或实现新功能。为什么这如此重要？因为使用这种方法，可以在不维护长期特性分支的情况下执行大的更改。你把自己从合并的麻烦中解放出来，你所做的对你的同伴来说是透明的。

让我们一步步来看看我们是如何实现[镀金玫瑰形](https://github.com/emilybache/GildedRose-Refactoring-Kata)的！

# 步骤 1: [提取 for 循环](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/65a26523958d1c6b7eb2f9e8d81cf80492a8adc8)的主体。

这一步相当明显。我还改变了迭代的发生方式，所以我不再通过索引来引用元素，而是改为基于范围的`for`循环——这一步需要将 C++版本升级到 C++11。

# 第二步:[实施非特殊项目的质量和销售行为](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/ac069ef55e1a5cd5a0c512faad6b104ecc4bbc30)。

它来了，抽象分支。我们介绍一个大`if-else`。

```
if (item.name != "Ragnaroos" ...) {
  // freshly implemented behaviour
} else {
  // old code
} 
```

如果该项目是非特殊项目，则使用新代码，但在所有其他情况下，仍执行旧行为。

# 步骤 3: [将更新移动到项目类](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/ce61820212f68e536ef189675a253edd65c786d1)

因为`quality`和`sellIn`是项目的属性，所以在`Item`对象中维护它们是有意义的。在这一点上，我们可能会尝试引入诸如`decreaseQuality`和`decreaseSellIn`这样的方法，但是这将意味着一个相当短期的死胡同，所以最好坚持使用更抽象的`updateQuality`和`updateSellIn`名称。

# 步骤 4: [实现“拉格纳罗斯之手”特殊项目的行为](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/c63ed5e9a9406582b82f4833447b3ab074900885)

根据规格，*硫磺*不会老化，质量保持不变。与他们的属性无关！如果你向前跑，这里已经有机会重构了，但此时此刻并不真的需要。所以代码就这么简单:

```
if (item.name != "Sulfuras...") {

} 
```

# 第五步:[实施陈年布里干酪的行为](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/f47e38300412b33ec0b3a3df1dfacf3b481f8578)

随着时间的推移，普通食品的质量会下降，而*陈年布里干酪*的质量会上升，而且速度不一样。这意味着我们不能简单地重用`Item::updateQuality`。此时，我们在`processItem`方法中实现了行为。如果你深入观察，虽然测试通过了，但是实现并不完全符合规范所说的。或者可能说明书写得不太好。谁知道呢？这一次，我决定保持现有的行为。

这是事情开始变得复杂的时候。

对于非特殊项目，行为完全封装在`Item`类中。对于*硫磺*和*陈年布里*，该行为在`GildedRose::processItem`功能中。很明显，这并不是最优的，最好在`Item`类中实现所有不同的行为。

一种选择是用虚拟的`updateQuality`和`updateSellIn`方法使`Item`成为基类，但是我不喜欢这个想法。这似乎不是一个小的重构。除此之外，我还提醒了自己[利斯科夫替代原理](https://en.wikipedia.org/wiki/Liskov_substitution_principle)。每当需要一个`Item`时，我就不能使用一个`AgedBrieItem`，因为`AgedBrieItem`不会扩展而是改变默认行为。然而最大的问题是实例化的改变。更新所有测试的负担，想象一下如果我们的客户正在使用`Item`类...

我组织 dojo 的同事向我们展示了另一个适合这类问题的想法。将变化的实现细节隐藏在另一个类中，这样我们就不必将 Item 转换成一个公共的父类。我们甚至不必改变项目的实例化方式。听起来对我们来说足够好了。它来了。

# 步骤 6: [将行为处理提取到一个`Updater`类](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/32b9b235d242fee994cac6bf4f394fbc73a52dad)

因此，虽然项目仍然以相同的方式实例化，具有名称、质量和销售日期，但其内部结构发生了变化。是的，你的类的大小改变了，你的客户将不得不重新编译，但我认为这是越来越少的问题。另一方面，他们将不必改变他们的代码，因为您只修改了您的内部结构。

在`Item`类的构造函数中，或者从构造函数调用的方法中，基于项目名称将创建一个`Updater`。

然后`Item::updateQuality()`和`Item::updateSellIn()`会将工作委托给`Update`类的相应方法。

为了不违反利斯科夫原理，我们不应该使用继承。在这个用例中，派生类不会扩展基类的行为，它们只是简单地改变它，这违背了我们的原则。

和 C++一样，没有内置的接口概念，我创建了一个抽象基类，只包含纯虚函数——除了构造函数/析构函数。然后我创建了前三个 Updater 类，分别是 DefaultUpdater、RagnarosUpdater 和 AgedBrieUpdater。

```
class Updater {
 public:
  Updater(int& sellIn, int& quality) : _quality(quality), _sellIn(sellIn) {}
  virtual ~Updater() {};

  virtual void updateQuality() = 0;
  virtual void updateSellIn() = 0;

 protected:
  int& _quality;
  int& _sellIn;
}; 
```

在 Updater 类真正达到这一点之前，我经历了许多迭代和提交，我必须解决一个严重的错误，我将在另一篇博客文章中详细介绍。

# 步骤 7: [为其余的](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/48ae24e02ed93d587331eaff1f2dba63d5e91850)创建更新类

在这一点上，我仍然必须实现两个更新类。一个用于后台通行证，一个用于召唤物品，这是一个新功能。在这一点上，这些只是手工练习。

# 第八步:[删除代码的原分支](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/fcfa2ca3826f865c03bfea33808d6701c64add28)

你可能已经注意到，直到这一步，我的大 if-else 只是在`GildedRose::processItem`中增长，这是不必要的，但我不想插手。相反，我现在完全移除它。因此，整个函数只有两行长。

```
void GildedRose::processItem(Item& item)
{
  item.updateSellIn();
  item.updateQuality();
} 
```

# 步骤 9:要做的任何清理工作

我们已经完成了大部分的重构和新特性的实现。让我们寻找其他的重构方法。

`GildedRose`类看起来挺好的，但实际上我觉得不需要`processItem`。它不应该知道`Item`的哪两个函数必须被调用，也不应该知道调用的顺序。`GildedRose::updateQuality`似乎是一个很不好的名字。

一旦[完成了](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/fcfa2ca3826f865c03bfea33808d6701c64add28)，我决定清理`GildedRose.h`，在某种意义上，我将每个类定义移动到它自己的头中，并将实现移动到相应的源文件中。到目前为止，在一个文件中工作是很方便的，但是是时候把它们移动到它们应该在的地方了。在我们能够正确使用 includes 和 forward 声明之后，它将为我们提供进一步重构的可能性。

这一步还要求[修改我们的 Makefile，以将所有新文件包含到 build](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/345e0e7ae173976af963dff31d9cfb7345e5782f) 中。

最后，我可以[从`Items`构造器](https://github.com/sandordargo/GildedRose-Refactoring-Kata/commit/0f94efb095c8562fdc52574e3325181b15279599)中移除`Updater`的实例化，并将它移动到`Updater`接口/抽象类中的静态工厂方法中。

我可以看到一些其他重构的可能性，但在某一点上，必须停止。我停在这里。

## 外卖食品

我已经练习过几次[镀金玫瑰形](https://github.com/emilybache/GildedRose-Refactoring-Kata)，尽管每次都有点不同，但这是迄今为止最有趣的一次。

对我来说，最有趣的概念是:

*   把工作委托给另一个类(层次结构)，这样你就不必让你的客户面对一个新的类层次结构，而不是他过去拥有的一个类。因此，我可以一直保持实例化不变。我不需要改变现有的测试。

*   我使用了分支抽象背后的思想。新代码用于我已经完成重构/重新实现的部分，而我根本没有碰旧代码。最终，我可以一次性删除所有旧代码。对于实现更大的迁移或进行大规模重构来说，这看起来确实是一样的。

我鼓励你做[镀金玫瑰形](https://github.com/emilybache/GildedRose-Refactoring-Kata)并记录下它是如何进行的。

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/08/08/gilded-rose-revisited)上。*