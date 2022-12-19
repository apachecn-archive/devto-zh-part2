# 用 Ruby pt.1 进行面向对象编程

> 原文：<https://dev.to/stefannibrasil/object-oriented-programming-with-ruby-pt1--a6f>

我去年在我的大学有 OOP，它是一个扫兴的人。回过头来看，我可能学到了对开发者最有价值的技能:做一名开发者==一名全职自学者(我刚刚发明的一个新概念)。

但是自从我去年从大学退学并开始实习以来，我觉得有必要回归基础，并最终能够使用 OOP 基础编写出好的代码。我开始了一个 [Rails 教程](https://github.com/stefannibrasil/depot_tutorial)并且我注意到我从来没有在我的终端上运行过 Ruby 程序(没有 Rails )!

从这次经历中我学到最多的是，我应该在学习框架之前先学习基础，例如:先学习 Ruby，再学习 Ruby on Rails。我想了解为什么 Ruby 变得如此流行，是什么让 OOP 成为计算机科学中如此重要的范例。

我在网上找到了很多资源，其中之一是麻省理工学院计算机科学，它提供了计算机科学和 Python 编程的完整介绍，我决定用 Ruby 做一些笔记。如果你也想试一试，OOP 会在第 14、15 和 16 课中给出。我还找到了[文档——编程 Ruby](http://docs.ruby-doc.com/docs/ProgrammingRuby/) ,这是一份完整的学习材料。这两个材料都帮助了我的学习，并指导了这篇文章:)

这是第一部分，将介绍 OOP 的基本概念，比如类和对象。我还在 GitHub 上建立了一个知识库，在那里我应用我所研究的东西来保存记录。

### 抽象一切

面向对象程序设计在计算机科学中并不是一个新概念，但它只是在 70 年代才变得非常流行。但是我们为什么需要面向对象程序设计，为什么它变得如此流行？引用埃里克·格里姆森教授的话:

> 当您试图对由大量以各种方式交互的单元组成的系统进行建模时，面向对象编程非常有用。

这就是为什么 OOP 被如此广泛地使用，并解决了我们现实生活中的大量问题，因为我们可以用这种范式解决几乎所有的问题！通过收集一些数据，它允许您编写更好的代码，这意味着更容易测试和重构。

## 让我们回到最基本的

每个开始学习 OOP 的人都会听到很多关于类、对象和实例的东西。让我们从理论上探讨一下。

#### 类、对象、实例

你一定听说过在 Ruby 中，*一切*都是对象:类是对象；实例是对象，对象是...我想你明白了！尽管在我们的现实世界中，我们会处理大量的对象，但是很难将其抽象成代码——至少对我来说是这样。但是一旦我们理解了它的目的和它背后的概念，它开始变得更有意义，事情开始变得更容易#希望如此。

[![We are all objects](../Images/ce80ba355b06ea704207e3478ae86486.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OC5UCd3L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ugtzzpozevd1dujrkr9.jpeg)

一个**类**是一组**数据**和**函数**的集合，它们在一起是有意义的，因为它们有共同的特征。它通过防止您将代码分散到不同的文件中来帮助您的日常工作。

正如埃里克·格里姆森教授在他的一堂课上所说的，如果把一个类想象成一个 stamp 就更简单了:你可以用它的 stamp 打印一个类的几个对象。使用戳记创建的每个新对象将具有相同的特征，但它们仍然是不同的对象，因为它们是该戳记的唯一副本，它们被随机分配到内存中的不同位置。

为什么物体如此有用？因为我们可以随心所欲地操纵它们！如果人类不再操纵周围的一切，会是什么样呢？！

但是如何将类定义应用于对象呢？当你创建一个新对象时，它会带有一些数据。您正在实例化一个来自特定类的新对象。你可以把这个对象的数据称为**属性**。例如，您可以在 initialize 方法(一个特殊的类方法)中定义它们。

您真的可以使用与其类相关联的函数来根据您的需要操纵它们。你可以称它们为**实例方法**。现在，让我们创建一个简单的类来看看这一过程。

所以实例和对象几乎是一样的，你想怎么称呼它取决于你:)

#### 显示代码

我们将从 animal 类创建 Animal 实例(你也可以在 [GitHub](https://github.com/stefannibrasil/RubyFarm/blob/master/lib/animal.rb) 上看到代码)。但是在深入研究代码之前，让我们稍微考虑一下实现。假设我们需要创建一个动物类，这样就更容易创建许多可爱的动物来玩。

动物有什么？它是做什么的？(我们现在不需要考虑所有的事情，我们可以随时添加/删除任何关于我们班级的信息)。

我们知道动物有名字、年龄、体重和接种日期。这些就是属性。我们也知道动物需要每年接种疫苗，睡觉和玩耍。这些是对象/实例方法。我们将编写一个方法来检查动物是否已经接种了疫苗，因此，如果创建了一个疫苗接种过期的新动物，我们将收到一个警报。

我们可以把这个放到一个名为 animal.rb:
的文件中

```
class Animal
  # The initialize method is a special Class method.
  def initialize(name:, age:, weight:, last_vaccine_year:)
    @name = name
    @age = age
    @weight = weight
    @last_vaccine_year = last_vaccine_year
  end

  def is_vaccinated?
     current_year = Time.now.year
     return true if current_year - last_vaccine_year <= 1
     "#{name} has not been vaccinated this year yet"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么

这个简单的 Animal 类工作得很好，但是它很一般，你不这样认为吗？。如果我想养猫、狗、鸟、鱼、马怎么办？我需要考虑像生物学中的物种这样的课程...很酷，对吧？但这将是我们讨论继承和封装的下一个主题。

如果您有任何问题或想要建议/贡献，请在此随意评论。我也在学习 Ruby，所以欢迎任何建议。回头见！