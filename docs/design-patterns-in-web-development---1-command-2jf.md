# Web 开发中的设计模式- #1 命令

> 原文：<https://dev.to/shikaan/design-patterns-in-web-development---1-command-2jf>

#### 查看更新版本[此处](https://withbenefits.dev/design-patterns-command/)

# 简介

正如介绍中所透露的，第一篇文章将是关于*命令模式*。这个模式是你可以在[四人帮](https://en.wikipedia.org/wiki/Design_Patterns)中找到的经典模式之一，它属于被称为**行为模式**的一套模式。

## 行为模式

顾名思义，行为模式关注对象的行为。

[![Zoidberg](img/44f0cb4799aee395321f640d446ea951.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TtzcJzLp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.quickmeme.com/img/b1/b1ee54106b29a0ee085bd3918f98e95d559e9ad70e4d23587cc97c66a51d5bf4.jpg)

与其他类型的模式不同，行为模式不仅是对象和类的模式，也是它们之间的通信模式。它们的主要目的是使用旨在简化复杂控制流的抽象在应用程序中的组件之间概括和分配职责。

这最后一句足够复杂，值得一个现实生活中的例子。

假设你在一家餐馆，你想吃一块多汁的 T 骨牛排(我想现在很明显我有东西吃了)。一种方法是站起来，走进厨房，让厨师为你准备一块牛排。这时，你意识到厨房里满是有着同样聪明想法的人，他们最终在厨房员工中制造混乱和困惑。只有一件事会更糟:你的前女友/男友，是的，就是那个对毒药情有独钟的人，是厨师。

[![Sad Panda](img/9fd548ee2e680f9c244f530fa0c30d39.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6ixxamAQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/originals/c4/b8/83/c4b8834dc60dd504f287b7a1232bc34e.jpg)

事实上，顾客只对获得食物感兴趣。与厨师的直接交流没有达到这个目的，实际上只会带来问题。同时，当您有多个请求时，这种直接通信不能伸缩，甚至当这些请求有多个侦听器时也不能伸缩。这是一个很好的例子，说明了*耦合*会给软件开发带来的问题。

好消息是，甚至在软件开发发明之前，人类就已经找到了解决这个讨厌问题的方法:下订单。

为了便于讨论，让我们假设厨房门上有一个邮箱。每当你想吃东西的时候，你只需要把你需要的东西写在一张纸上，然后邮寄你的订单。

这个简单的技巧神奇地解决了我们的问题。我们没有被强迫去知道谁在煮我们的食物。例如，我们甚至不知道是否有人真的在烹饪我们的食物，或者他们是否购买-转售。这意味着灵活性的巨大增加(也许会对以这种方式工作的餐馆失去一点信任)。此外，这改善了厨房的整个流程，因为他们可以区分优先顺序，同时准备，扔进垃圾箱，记录或做任何他们想做的事情。

在之后，每个人(包括熊猫)都过上了幸福的生活！

哦，顺便说一下，这是命令模式。

# 命令模式

**[出示代码](#code-examples)**

## 这是怎么回事？

让我们从独一无二的戈夫的一句话开始。

> **意图**
> 将一个请求封装成一个对象，从而让你用不同的请求参数化客户端，队列或日志请求，并支持可撤销的操作。

实质上，Command 就是将一个例程封装在一个对象中。在上面的例子中，我们将对食物的请求封装在一个对象中，这个对象就是用来下订单的那张纸。封装的对象就是我们所说的`Command`，因此该图案的名称为 [<sup>2</sup>](#note2) 。

## 效果

应用命令主要有两个作用:减少调用方和命令执行方之间的耦合，使一个例程成为第一类对象。

上例中的 ex 场景应该足以让您相信，即使在计算机科学之外，耦合也是危险的。

[![Overly attached girlfriend](img/e84bd6751efc6e7d24142e54bc3a4c42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TS81OE_I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.quickmeme.com/img/01/019c87c5b8b608e863093dbcaa88101564d0fab0b9cdbd2a780cbb940e94a782.jpg)

如果你没有心情去想你那些疑神疑鬼的熟人，你也可以考虑一下，如果你的饭需要由两个团队来做，一个团队专门做牛排，一个团队做配菜，你必须完成的程序基本上是不变的。

与此同时，厨房工作人员并不关心订单是否来自服务员、来自电话、订单或其他任何东西。只要他们收到可以执行的命令，他们就没事。

这只是我们在转换对象中的例程时获得的一部分收益。最棒的是...(要说出令人吃惊或高兴的事情)听着...他们是物体！这意味着您可以将例程作为对象来操作，例如您可以存储它们以获得事务历史记录，您可以延迟执行，如果 s**t 从管道中出来，您可以忽略它们，您可以扩展它们以添加调试检查，等等！

## 牛逼！我有生之年会需要这个吗？

号码

[![Just kidding](img/3740aad05a837b3af84d5872ab9f4812.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OloVNEfG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sayingimages.com/wp-content/uploads/yo-dawg-im-just-kidding-meme.jpg)

在某些情况下，**命令**不仅极其方便，而且几乎是必需的。

### 回调

每次命令的执行者和发布者不仅彼此不认识，而且他们*也不能*事先认识对方。

假设你正在开发一个奇特的 UI 工具包。你当然是在开发需要重用的东西，所以如果你构建一个`Button`组件，你希望它能够执行*任何*动作，你不想硬编码一个。

“嘿，老弟！我们有试镜！”是的，我知道，但并不是世界上的每个人都如此幸运地在日常工作中使用 JavaScript(抱歉，有偏见的评论)。当你想(或者必须)严格面向对象时，这是实现回调的方法。

### 交易和日志

将所有命令作为第一类对象允许您存储它们，从而创建事务的历史。

这在你需要交易历史的系统中非常方便，比如银行。此外，您还获得了另一个令人愉快的副作用:您可以在任何时间点重建系统的状态，只需回溯事务历史，如果发生问题，您的生活会变得非常轻松。

当然，您也可以反过来做:您可以将命令列表作为要执行的任务的队列，而不是在执行完命令后存储它们作为已经发生的事情的参考，就像在餐馆示例中一样。

如果您需要更多的“劳动力”，您只需要为该队列添加更多的消费者，使您的应用程序在整体上更具可伸缩性。

### 退回/准备

使一个动作的执行成为一个对象，允许你用两种方法创建一个对象:`execute`和`undo`。前者意味着做某事，而后者则意味着撤销你刚刚做的事情。

把以上关于交易的内容加起来，你就可以很容易地建立和撤销/重做历史。

## 码前的最后努力...

在深入代码示例之前，我们需要建立一些术语，这样我们就可以相互理解。我将使用与 GoF 中完全相同的语言，所以如果你想从那里开始，这将会更容易。

这种模式的参与者是:

*   **接收器**:
    *   知道如何执行命令；
*   **命令**:
    *   声明用于执行操作的接口；
*   **具体命令**:
    *   定义接收器和要执行的操作之间的绑定；
    *   调用接收方的方法来完成请求；
*   **客户端**
    *   创建具体的命令并设置其接收者；
*   **调用者**
    *   发出执行命令的请求；

在餐馆示例中，我们有:

*   `Cook`作为*的接收者*
*   `Order`担任*具体指挥*
*   `Restaurant`作为*客户*
*   `Customer`为*调用者*

一些伪代码看起来更严重一点:

```
interface Command {
    function execute()
}

// Concrete Command
class Order implements Command {
    Cook cook;
    Meal meal;

    execute() {
        cook.prepare(meal);
    }
}

// Receiver
interface Cook {
    function prepare(Meal meal)
}

// Invoker
class Customer {
    Order order;
    Meal meal;

    mailOrder(Order order) {
        order.execute()
    }
}

// Client
class Restaurant {
    Cook cook;
    Customer customer;

    main() {
        order = new Order(cook, customer.meal)
        customer.mailOrder(order)
    }
} 
```

# 代码示例

> 你可以在这里找到这些例子的更详细版本
> 
> ## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)/[图案](https://github.com/shikaan/design-patterns)
> 
> ### 设计模式在实际代码中的使用示例
> 
> <article class="markdown-body entry-content p-5" itemprop="text">
> 
> # 设计模式
> 
> 设计模式在实际代码中的使用示例
> 
> 这些是本系列文章的参考资料
> 
> </article>
> 
> [View on GitHub](https://github.com/shikaan/design-patterns)

## 前端:UI 套件

按照上面的第一个例子，下面是一个简单的例子，说明如何在前端使用命令模式。我选择不使用任何框架，因为这个想法足够通用，也适用于普通的 JavaScript。

在这个例子中，我们将创建并呈现一个`Button`组件(Invoker)，它将执行一个`OpenAlertCommand`(具体命令)。窗口(接收方)实际上负责完成工作，而应用程序(客户端)负责包装一切。