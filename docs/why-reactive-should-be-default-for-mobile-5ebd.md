# 为什么被动应该是移动设备的默认设置

> 原文：<https://dev.to/xuhaibahmad/why-reactive-should-be-default-for-mobile-5ebd>

在软件的早期，编写代码的方式非常简单。你可以从程序中的一个入口点开始(通常是一个 **main** 函数)，依次执行指令，然后优雅地结束程序。这很简单，也很容易概念化。

由于线性控制流，不仅管理程序的[状态](https://en.wikipedia.org/wiki/State_(computer_science))更容易，而且测试和调试也更容易，因为所有可能的结果都是可预测的。这种强制性方法的成功有助于在未来几年中建立编程思维方式。今天，我们大多数人都喜欢以命令式的方式思考，不管我们使用的是什么平台或技术。

## 命令式方法在现代系统中失败了

> "所有现代系统都是异步的."

乍一看，这似乎有误导性，但是如果你仔细想想，今天几乎每个系统都要求我们以非阻塞的方式编码。我所说的无阻塞是指，您需要尽可能地消除系统中的等待时间。例如，在处理网络请求时向用户显示负载指示器是不理想的。尽管许多人仍然遵循这种模式，但是从用户体验的角度来看，这是一个灾难。

> "没有人喜欢等待，如果不能马上得到服务，他们就会离开。"

在移动平台上，情况变得更糟，操作系统本身是无数外部变化的来源。你可以翻转你的设备，它会破坏你的应用程序，除非你增加了 3 倍以上的代码来处理一个旋转场景。然而令人惊讶的是，尽管本质上是异步的，但几乎所有这些流行的移动平台都没有被设计成反应式的。

## 无功编程

嗯……那么反应式编程到底是什么？让我们从维基百科得到一些帮助:

> "在计算中，反应式编程是一种与数据流和变化传播有关的声明式编程范例."

[![Wut?](../Images/472b258ec369dc8717ee347c4dd916b6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LGJFVGeO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/SqmkZ5IdwzTP2/giphy.gif%23center)

没关系！假设你有一个程序，其中有一个按钮和标签。它们被定义为类，就像面向对象系统中的其他事物一样。这个程序所做的就是将标签的文本更新为“Clicked！”单击按钮时。所以我们可以说，按钮可以以某种方式影响标签内的文本，对吗？

[![OO Relation](../Images/86d58412def16567b5672ee72b304944.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AZ8LCFHh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zuhaibahmad.com/assets/images/posts/relation.png%23center)

所以情况很简单，按钮可以被点击，标签可以被更新，两者都可以在各自的类中定义为一个函数。现在，这里有个问题！如果您要编写这个交互的代码，那么当按钮被点击时，您会把更新标签的逻辑放在哪里呢？是在纽扣本身里面还是标签里面？

假设你使用按钮，顺便说一句，我们通常是这样做的，那么在这种情况下，你需要在你的按钮中有一个标签的引用，你可以在按钮被点击时更新它。这本质上是一种主动的编程方式。我们基本上把状态管理的所有责任都委托给了按钮，让标签保持被动，也就是说，不关心外面发生了什么。互动的时候按照说明就好了。

[![Proactive Relation](../Images/4abaccfe32529f56038cfb06b1bde2e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d5QY_iAp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zuhaibahmad.com/assets/images/posts/proactive_relation.png%23center)

类`Button`看起来会像这样:

```
class Button (val label: Label) {
  public fun onClick() {
    label.setText("Clicked!")
  }
} 
```

这种方法的替代方案是反应式编程。我们基本上只是颠倒了角色，这样状态所有者就有能力改变自己，让按钮要求改变，标签对它作出反应。

[![Reactive Relation](../Images/52210a2c237396a9a78da7119f1b96fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2kLVhCc1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zuhaibahmad.com/assets/images/posts/reactive_relation.png%23center)

在这种情况下，`Button`类应该与此类似(注意这里绝对没有标签或任何外部依赖的痕迹):

```
class Button {

  interface ClickListener {
    fun doOnClick()
  }

  var listener: ClickListener? = null

  public fun onClick() {
    listener?.doOnClick()
  }

  public fun addOnClickListener(l: ClickListener) {
    listener = l
  }
} 
```

而`Label`将携带用于声明其管理的所有代码以及所有注册客户的列表。

```
class Label (val buttons: MutableList<Button>) {

  private var text = ""

  init {
    buttons.forEach {
      it.addOnClickListener { 
        this.setText("Clicked!") 
      }
    }
  }

  private fun setText(text: String) {
    this.text = text
  }
} 
```

> 但是为什么要反应呢？这种方法有什么变化？

首先，[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)也就是标签类现在完全负责管理自己的状态，并且可以很容易地对外界隐藏所有的实现细节。

第二个相关的问题是，现在我们只有一个改变的来源。我的意思是，以前我们必须将`setText`方法公开，所有的客户都可以随时调用它，这意味着增加了复杂性。现在我们有了一个隐藏的`setText`方法，它只能从一个地方调用，也就是在`Button`的`doOnClick`回调中，你可以使用`addOnClickListener`回调将任意多的客户端连接到它。这导致[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)和简化的代码库。

## 无功扩展

如前一节所述，[回调](https://en.wikipedia.org/wiki/Callback_(computer_programming))是反应式编程的关键促成因素。然而，这种做事方式会导致一种非常不舒服的情况，称为[回调地狱](https://stackoverflow.com/.../what-is-callback-hell-and-how-and-why-rx-solves-it)。[反应式扩展](//reactivex.io/)(缩写为 RX)是一个帮助解决这类问题的框架。

库的基本构件是**可观察的**、**观察者**和**操作者**。如果您熟悉[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)，这基本上是相同的模式，只是有一些改进。你让事物变得可观察，让其他事物观察这些可观察，在可观察产生一些数据而可观察接收到数据的过程中，你应用一些操作符在接收端将数据塑造成你喜欢的形式。

要了解关于这个框架的更多信息，请查看 Kaushik Gopal 的这个[演讲或者 André Staltz 的](https://www.youtube.com/watch?v=k3D0cWyNno4&t=1s)[指南](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)。

## 移动& RX 移动

既然我们已经了解了反应式编程及其助手框架。问题是，这如何解决我在本文开头提到的与移动平台相关的问题？

与其给出我自己的长篇大论，我宁愿把你引向 Jake Wharton 的精彩演讲，他在演讲中讨论了 Android 环境下的这些问题以及使用 RxJava 解决这些问题的方法。

关键是基本上不要讨厌自己，去异步，让世界处理他们自己的问题，而不是让你的代码以主动的方式依赖于外部的变化。

## 结论

状态管理很难，尤其是在移动平台上。虽然理想情况下 SDK 本身应该是反应式设计的，但我们不应该让这成为我们应用程序中不可靠性的来源。多亏了 RX movement，我们拥有了几乎所有知名语言的反应式扩展端口。特别是在 Android 的背景下，Kotlin 的崛起提供了一个很好的机会，最终开始以功能性和反应性的方式编写我们的应用程序。

* * *

图片来源-[react vex](http://reactivex.io/)。

如有建议和疑问，只需[联系我](http://linkedin.com/in/xuhaibahmad)。