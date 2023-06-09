# 一个好的包结构如何产生一个好的应用程序

> 原文：<https://dev.to/skuzzle/how-a-great-package-structure-can-make-a-great-application-22ng>

在这篇文章中，我会给你一些简单的建议，如果你认真遵循，将会立即为你的 Java 应用程序带来一个易于维护和可扩展的架构。虽然这篇文章专门讨论 Java，但是其中的概念同样适用于其他允许在包中安排代码的语言。

人们可以写关于如何实现好的架构的书(人们也这样做了),但是你现在需要一个更好的架构，而且时间紧迫——我也是——所以让我们开始吧。我现在要告诉你的一切都可以归结为一条最重要的建议:

> 自由使用*包私有*范围

如果你这样做了，它会立刻解决很多问题，一旦你习惯了，你会像我一样惊讶于这个建议的价值。我来详细解释一下这个。

在更大的应用程序中，可维护性的关键是控制
应用程序的单个组件的*耦合*。这假设您的应用程序必须首先被结构化为组件。如果不是，你可能已经经历了某种*包间依赖地狱*和包周期之类的东西(好 ol’*大泥球*)。

> 依赖性是所有规模的软件开发中的关键问题

Java 提供了*包*的概念，将你的代码组织成组件。但是经验告诉我，大多数情况下，一个单独的组件被分散在多个包中。当程序员在*技术*方面下选择他们的包时，就会发生这种情况。例如，如果你有一个包`org.domain.app.controllers`存放所有的控制器，另一个包`org.domain.app.services`存放所有的服务，你就有了一个技术包结构。在这种情况下，可能不相关的类在同一个包中彼此靠近，而相关的类位于远处。

> *相关代码的局部性是代码易于推理的一个重要方面*

因为共同解决特定领域问题的相关类位于不同的包中，所以它们都必须是*公共的*才能一起工作。通过选择不把相关的类放在同一个包中，你就把自己暴露给了所有的罪恶，把某些类用在了你不想用的地方。

如果你事先选择了一个与*域*或*特性*相关的包结构，首先有一个简单的解决方案可以避免这个问题:

> 给予尽可能多的类*包私有*范围

实际上，默认情况下，你应该使每个类*包私有*，并且只在必要时扩大它的范围。一段时间后，每当你看到公开的事情*，你就会产生不好的感觉——这是一件好事。它让你明确地考虑你的包的公共接口。遵循此建议，您将自动创建具有以下特征的包:*

 **   高内聚:包中的所有东西都与其他东西有某种联系。相关代码的局部性将使推理变得容易。
*   与其他包的低耦合性:因为你的包只有一个最小的公共接口

虽然这些都是好事，但你也会经历你的包裹变得越来越大，大到让你感到不舒服的程度。

[![No packages - No cycles](img/f3470edfffdc8304038ee4884690ec9e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H0M40SP7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k4r03z4fbqiwgxbd6izm.jpg)

如果你觉得你的包变得太大，这是一个明显的迹象，可能有一个更好的特定领域的削减。在这种情况下，您应该回顾并质疑当前软件包的整体目的。根据我的经验，你会遇到以下情况:

1.  你的原始功能被实际上服务于不同功能的无关代码破坏了。
2.  随着时间的推移，原始特性的需求发生了很大的变化，以至于原始结构不再适合。也有可能特性没有改变，但是你在编码的时候对它的理解改变了。

大多数情况下，第一种情况很容易解决。如果你真的处于这样一种情况，大部分不相关的代码溜进了你的特性包中，那么把它隔离出来并转移到它自己的包中应该是相当容易的。

如果隔离较小的部分不那么容易，你可能会遇到第二种情况。这是*包私有*的魔法达到极限的地方，你将不得不做实际的工作。现在，您需要在包级别上进行一些重构——也就是说，您需要将当前的包拆分成多个新的包，以便更好地满足您试图解决的领域问题的需求。这很可能无法通过在包之间移动一些类来解决，但可能需要实际的代码更改。

但是因为所有相关的东西都在一起，而且你知道大多数东西都不在包外使用，这给了你重构的信心。

[![unit tests](img/5d9f13980edf4507d098973a29e93155.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OwUb5deH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/67q07eyq8e3sd0n8p31v.jpg)

你有没有想过单元测试中的*单元*是什么？您可能遇到过测试套件，其中每个生产类都有一个测试类。如果是这样的话，你很可能经历过你的单元测试不是非常健壮的。如果您调整实现类，即使您实际上正确地保留了行为，它们也很容易被破坏。那是因为*单位*不等于*级*。*单元*与所有你想要测试的代码相关，以便解决一个领域特定的问题。*单元*意味着*特性*，在我们的例子中，特性意味着你的包的公共接口。

> 不要为每个生产类编写一个测试类。为每个特性编写一个测试类

这样做将您的测试代码松散地耦合到生产代码，就像您的生产代码松散地耦合到其他包一样。您可以自由地更改公共接口背后的所有实现细节(比如，甚至删除类),而不会破坏您的测试。这就是*信息隐藏*的真正含义。

总而言之，通过自由地使用包私有作用域，你可以获得(免费！)

*   您的包内的高内聚力
*   封装之间的低耦合
*   因为组件/包之间定义良好的接口，所以是一个持久的架构
*   更容易推理的代码
*   为了实现更健壮的单元测试，应该测试什么是一个很好的提示

再说一次:你得到这个只是因为在你的类声明中省略了`public`修饰符！每当你觉得需要创建一个新类的时候，就把它打包成私有的，并把它放到你认为最适合的地方。这使您能够随着时间的推移仔细地改进您的包布局。

细心的读者可能已经注意到了以下几点:上面关于构建你的包的建议实际上是我们在课堂上遵循了几十年的建议。毫无疑问，为了拥有一个定义良好的公共接口，最好的做法是将助手方法*保持为私有*。如果*“依赖性在所有规模上都是一个问题”*那么管理依赖性的解决方案在所有规模上看起来都是相似的。

更多信息:

*   西蒙·布朗关于“按组件打包”:[http://www . codinghearchitecture . com/2015/03/08/package _ by _ component _ and _ architecturally _ aligned _ testing . html](http://www.codingthearchitecture.com/2015/03/08/package_by_component_and_architecturally_aligned_testing.html)
*   [https://github.com/olivergierke/moduliths](https://github.com/olivergierke/moduliths)(一个 spring Boot 扩展，试图以这种方式构建你的应用程序)
*   关于 ddd 的一些事情*