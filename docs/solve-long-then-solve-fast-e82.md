# 求解长，则求解快。

> 原文：<https://dev.to/akira_knows_/solve-long-then-solve-fast-e82>

当我刚开始编程时，我爱上了用许多不同的方法解决同一问题的能力。当你第一次学习一个概念时，我也学到了长时间的单调乏味总是胜过短时间的捷径。

为此，我将向您展示几种不同的方法，在名为“Unicorn”的 Ruby 练习中，使用名为“attr_reader”的神奇 Ruby 工具，只编写两种方法。

[![](img/68cbe4f427e5f7e5303f7c86d6531630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3wTcUyjC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/186/1%2A-MxvO-39B_GBemYD87ljTg.png) 

<figcaption>这只独角兽象征着你的优秀</figcaption>

我用的是 Ruby 2.4.1，Minitest gem，Atom 文本编辑器，还有一个非常棒的字体，叫做“Monospace”。

在设置好基础之后，我准备好进行我的第一次测试。

让我们来看看:

[![](img/a79ee76f53cac1003edecbe5d5707153.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MqqA9Ak7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/777/1%2AqXpd6uNQIhtLbwHwxBpT-w.png)

基于 TDD 的指导原则，让我们运行测试，看看它会给我们带来什么样的错误。

[![](img/8573e06ebc679d977adc421b376d9da7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--42KxrMow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3t7yxB-wugb4Ykf6R96hcA.png)

好吧，挖吧。让我们创建一个名为<name>的方法，并引用我在初始化方法中创建的实例变量@name！</name>

[![](img/1d8bb1dbaefaae46c58ce74c4bf06e09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Cdu3lGh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/670/1%2AmkPKSnXCQPLbKvdcj0C7HQ.png) 

<figcaption>艳丽。太美了。我很痴迷。</figcaption>

让我们再次运行我的测试，看看它是否通过。

[![](img/ffd16d89e6e86cd0fb44fdf410ef665a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hPRzpNmf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/953/1%2AIzJ-pCn8KFPCy00YZMvJXg.png)

太酷了！它起作用了，我的测试通过了，我可以继续前进了——这是目前最重要的。

然而，经过进一步的检查，我们发现这个方法只需要三行代码就可以完成……一点都不需要。当然，这是一个 getter 方法，这意味着它向我显示了它所引用的数据，但是如果我需要写三行代码来读取实例变量@name 所指向的数据，那就要花很多时间。如果我想用这个类的其他属性做这件事呢？如果它有一个名字，一种颜色，一个产地，一个品种，一个…我不知道，其他独角兽的属性呢？那么，有没有一种方法可以做同样的事情，而不用为每个属性占用三行代码呢？我天生懒惰，所以我想弄清楚。

[![](img/1170b9b2849606b6c2a50952a642faa1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tPkcEihC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/794/1%2A3nHz08Nf1UDnqxUZHidINA.png) 

<figcaption>我们要死在这里了。</figcaption>

这就是 attr_reader 的用武之地。attr_reader 是像上面那样编写 getter 方法的一种简便方法。它还公开了您的数据，就像编写完整的方法一样。看起来是这样的:

[![](img/e294e38889c73daabdcb1f2e50559a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Au2Ta5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/737/1%2AxBXZ9Ggjdwx06hS5cXH_1g.png) 

<figcaption>用 attr_reader 代替写出一个<名>方法</figcaption>

使用这个工具，您可以简化我们之前编写的<name>方法，并添加其他方法，我们很快就会看到。因此，它让您不必一遍又一遍地编写所有代码。当我们的初始化方法中有很多实例变量时，这就变得特别好了。</name>

***提醒一句***

当我第一次开始编码时，人们告诉我“用 attr_reader 就行了！用 attr_reader 就好！!"所以我使用了它们，并不完全理解它实际上是做什么的。一位非常明智的导师帮助我明白了我不知道我到底在做什么，所以她让我先用手写出所有内容，然后让我使用快捷方式。我强烈建议你也这样做。从长远来看，完全投入和使用“捷径”会花费我更多的时间。

我们再次运行我们的测试，它通过了。

让我们看看下一个测试:

[![](img/78ee88203cd1c0536ab6e59c3c85ea39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gphPzInp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1280/1%2A9faudx-pm-0MAfNNS4c-wQ.png)

在运行这个测试时，我们看到方法<color>不存在:</color>

[![](img/ab66d4b8e6f838b43a0308fb0b4ba020.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eW2ooppg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbGgLMdj-5CPJFmy0j6bFnA.png)

我们来写吧！首先是“长”的方式，然后使用 attr_reader。

[![](img/80389243779d4c59617b857e818bf22e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lYkS_DTi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/406/1%2Acxzuj4ohK8KvkZn0gObPlw.png)

现在，使用 attr_reader:

[![](img/c5feba71e61997f39e4a046038ebf811.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tNDyCk3---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/774/1%2ANghCfd-G-m-VfCLE3s6S2w.png)

这两种方法都满足编写方法<color>的要求，该方法返回与实例变量@color 相关的数据。两者都将通过我们的测试，并进入下一个问题，这是另一个时间的另一个帖子！</color>

*但是等等，另一个警告*

如果你想在一个 attr_reader 中添加多项内容，除了最后一项，你必须在每一项后面加一个逗号。我的痛苦，你的收获。

记住，在你开始走捷径之前，先把它写出来几次——你不想在森林里迷路吧！除非树林里全是独角兽。在这种情况下，你想怎么迷路就怎么迷路。

[![](img/f0805b15ba33c35bbd74bd0a717b87d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_mSvxpbP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/215/1%2Aylc2E2vYh4TsFtbcJdkF-Q.png) 

<figcaption>独角兽玛格丽特为你骄傲</figcaption>