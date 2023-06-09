# 面向对象编程与希腊神话

> 原文：<https://dev.to/hector6872/object-oriented-programming-and-greek-mythology-6jg>

## 原理和规律

我们经常发现这样的代码，但我们并没有给它任何重要性，因为它很常见:

```
val model = Model(id = "whatever")

if (model.id.*isNotEmpty*()) {  // <<< look at this !!!
    doThis()
} else {
    doThat()
}

... where `model` is:

data class Model(
    val id: String = String(),
    val color: Color = Color.RED
) 
```

Enter fullscreen mode Exit fullscreen mode

好了，[继续前进，这里没什么好看的，请大家散去](https://www.youtube.com/watch?v=rSjK2Oqrgic)。

然后，例如，业务需求改变，我们还必须检查它的`color`是否等于`Color.GREEN`来验证该模型，迫使我们在整个项目中寻找那个`if`来更新它，浪费时间，对一堆文件(*又名[猎枪手术代码气味](https://refactoring.guru/smells/shotgun-surgery)* )进行更改和/或在其他地方破坏某些东西。

最后，我们会祈祷那些要求不会再改变。但是他们总是可以改变的。

这里的问题不是需求，而是设计:[为什么我们仍然在问而不是告诉？](https://martinfowler.com/bliki/TellDontAsk.html)

### 什么是告诉，不问(TDA)原则？

> 程序代码获取信息，然后做出决策。面向对象的代码告诉对象去做事情。
> ——亚历克·夏普[《用例子说话》](https://www.amazon.com/Smalltalk-Example-Developers-Alec-Sharp/dp/0079130364)麦格劳·希尔公司，1997 年

在对象之外做决定违反了它们的封装，所以**不是[询问对象的状态](https://www.martinfowler.com/bliki/GetterEradicator.html)然后做决定，我们应该告诉对象做什么。**

> 告诉是命令，询问是询问。

我们应该停止到处使用臭名昭著的[贫血的领域模型](https://en.wikipedia.org/wiki/Anemic_domain_model) [反模式](https://www.martinfowler.com/bliki/AnemicDomainModel.html)，**开始把数据和操作数据的方法放在同一个地方** :

```
val model = Model(id = "whatever")

if (model.isValid()) {
    doThis()
} else {
    doThat()
}

... where `model` is:

data class Model(
    val id: String = String(),
    val color: Color = Color.RED
) {

    fun isValid() = id.isNotEmpty() && color == Color.GREEN
} 
```

Enter fullscreen mode Exit fullscreen mode

通过进行上面所示的更改，我们隐藏了实现细节，使我们的代码更容易理解，并且[维护](https://en.wikipedia.org/wiki/Don't_repeat_yourself) : **下次围绕该模型的任何更改都不会影响我们项目的其余部分**。

### 原则不是法律

[![](img/2da8b0acee6648a7155c56ac5f48887e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7ImY5FFX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/6-greek-oop/art/1.jpeg)

我们必须始终牢记封装，但有时我们必须在使用 **TDA** 或不使用之间找到一个平衡点，因为严格遵守它就像它是一条定律一样有一些缺点:

*   **类往往更大，**增加了它们的复杂性，使它们更难处理

*   责任增多 ( *“权力越大，责任越大。”— [本大叔](https://en.wikipedia.org/wiki/Uncle_Ben#%22With_great_power_comes_great_responsibility%22)* )以及它们的耦合(在这里想想作为应用程序核心部分的典型对象:我们不需要上帝对象)

*   我们可能会违反其他原则，例如 [SRP](https://en.wikipedia.org/wiki/Single_responsibility_principle)

    > # Tell everyone that the principle of don't ask can't be treated like the rule of thumb (like many other principles) because we often have to ask questions.

## 德米特里定律

通常与 **TDA** 原理相关的我们经常会遇到[得墨忒耳](https://en.wikipedia.org/wiki/Law_of_Demeter)定律( *LoD 又名“最少知识原理】*)。

**LoD 可以总结为“物体间的友谊”**:物体必须是独立的，只和它的“最亲密的直系朋友”接触，“不要和陌生人说话”。

> 我朋友的一个朋友是…？(根据 LoD)不是我的朋友。

因此，要实现这种行为，我们必须遵守这个简单的规则:对象的任何方法都只能调用属于以下对象的方法:

*   它自己

*   它的参数(也是对象)

*   它创建/实例化的任何对象

*   其直接组成对象

```
class A {
    private val c = C()

    fun method(b: B) {
        b.method()
        c.method()
        Object.method()
        D().method()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

尽管有些人将这个*法则*归结为“点计数”(其中`model.getX().getY().doSomething()`是错误的，但是`model.doSomethingXY()`很好*，没有明确的原因*……)并且倾向于使用太多的包装类(这是当我们试图避免 **LoD** 时最常见的结果)，但是**我们应该将 LoD 更多地视为一个设计指南，而不是一个法则本身**。

> > Suppose you are in a store, and what you want to buy is worth $25\. Will you give the clerk $25? Or do you give your wallet to the clerk and ask him/her to get $25 back?
> > —Misco Herveri ["Speak in clean code—don't look for anything!"](https://youtu.be/RlfLCWKxHJ0?t=941)

让我们看看典型的报童例子(在[本文](http://www.ccs.neu.edu/research/demeter/demeter-method/LawOfDemeter/paper-boy/demeter.pdf)中有深入描述)。

下面是不遵循 **LoD** (或者 **TDA** ):
的代码节选

```
// given these classes

data class Neighbour(
    val firstName: String = String(),
    val lastName: String = String(),
    val wallet: Wallet = Wallet()

    ...
)

data class Wallet(
    var money: Float = 0F  // please forgive me :(

    ...
)

// when payday comes 

class Paperboy {
    companion object {
    private const val PRICE = 2f
    }

    private var earnings: Float = 0F

    fun getMoney(neighbour: Neighbour): Boolean =
    if (neighbour.wallet.money >= PRICE) {
        neighbour.wallet.money -= PRICE
        true
    } else {
        false
    }

    ...

}

The question here is: have you ever seen a paperboy 
taking money directly from a neighbour's wallet? 
```

Enter fullscreen mode Exit fullscreen mode

因此，为了遵循“真实世界”的场景，我们应该像这样使用 **LoD** (加上**TDA**):

```
// given these classes

interface Payer {
    fun pay(
    service: Service,
    amount: Float
    ): Boolean
}

interface Service {
    fun pay(amount: Float)
}

data class Neighbour(
    val firstName: String = String(),
    val lastName: String = String(),
    private val wallet: Wallet = Wallet()
) : Payer {
    override fun pay(
    service: Service,
    amount: Float
    ): Boolean =
    if (hasEnoughMoney(amount)) {
        service.pay(amount)
        true
    } else {
        false
    }

    private fun hasEnoughMoney(amount: Float): Boolean =
    wallet.money >= amount

    ...

}

data class Wallet(
    var money: Float = 0F // ok, I know :(

    ...
)

// when payday comes

class Paperboy(
    private var earnings: Float = 0F
) : Service {

    companion object {
    private const val PRICE = 2f
    }

    fun getPaid(payer: Payer): Boolean =
    payer.pay(this, PRICE)

    override fun pay(amount: Float) {
    earnings += amount
    }

...

}

Ok, now everything seems a little more complex but 
now the paperboy is a good guy who ask for his/her 
money and doesn’t take anything from anyone’s wallet :) 
```

Enter fullscreen mode Exit fullscreen mode

使对象之间的联系尽可能的松散(“让它们有点害羞”)将帮助我们提高灵活性并促进代码维护(由于隔离，任何未来的变化都不会对我们的代码库产生太大的影响)以及使我们能够在不使用大量[链式模仿的情况下测试它们](https://twitter.com/damianguy/status/4977743956?s=19)。

> # "True friendship is not inseparable, but not knowing what will change even if we are separated"- [TED](https://en.wikipedia.org/wiki/Ted_(film))

本文最初发表于[媒体](https://medium.com/@hector6872/object-oriented-programming-and-greek-mythology-14c9b1271e34)

* * *

[1]如果你对这句话的真正来源感到好奇:[https://quoteinvestigator.com/2015/07/23/great-power/](https://quoteinvestigator.com/2015/07/23/great-power/)
[2]“它之所以如此命名是因为它起源于 Demeter 项目，一种自适应编程和面向方面的编程工作。这个项目的命名是为了纪念[德米特里](https://en.wikipedia.org/wiki/Demeter)——[维基百科](https://en.wikipedia.org/wiki/Law_of_Demeter)
【3】“所有人的朋友都不是朋友。”—亚里士多德(也是希腊人)

* * *

[外部链接👀](https://gist.github.com/hrules6872/ad1a39005ce8eb16a665cc1e8812f8e7)