# 我“喜欢”反模式

> 原文：<https://dev.to/levimoreira/i-love-anti-patterns-1bj1>

每个人都在谈论设计模式，但是了解力的另一面就像拜访老朋友一样有启发性。当然，我不喜欢生产它们，而是喜欢学习它们。作为开发人员，我们总是试图提高自己，但是为了能够提高，我们需要犯错误，其中一些是我们在职业生涯中犯的错误。了解最常见的可以帮助我们避开它们。在这里我列出了我最喜欢的 5 个:)

## 5 -大泥球

[![Ball of mud](img/20ed65257801869d75920128b2a96b8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gOcSeHXc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.synthesis.co.za/wp-content/uploads/bfi_thumb/AgileArchitecture1-1rl3zo5ig9008zq8zgwie6cn9vnbl7um76y52b72jvbo.jpeg)

这是引用一段没有清晰架构的代码的好方法。我们中的许多人都为在人类历史初期用一种技术制造泥球而感到内疚。我记得当我开始使用 Android 时，我在活动中写了很多代码，并希望一切顺利。你有 BBM 的一些症状:

*   大量重复的代码
*   数据是完全分散的，在许多位置重复出现许多次
*   代码看起来像是被许多不同的人修补了许多次。

最初的术语是由布莱恩·福特和约瑟夫·约德创造的，它说:

> 一个大泥球是一个杂乱无章、杂乱无章、杂乱无章、用胶带和铁丝捆成的意大利面条式的丛林。

这很容易通过在我们的代码中使用架构或最低级别的组织来弥补。

## 4 -由委员会设计

[![alt](img/216054dffea08232fa60a848aea18a3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IeGDgS30--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.sutter-group.com/wp-content/uploads/2015/03/design-by-committee-featured.jpg)

A.又名:镀金，让每个人都开心
镀金是完美主义者的敌人。它只是想增加越来越多的功能，即使产品已经准备好了。这也是关于不必要的过于精细或复杂。发生这种情况的一些症状:

*   会议花费了过多的时间，而且感觉不到对项目重要方面的把握:
*   设计开发比最初计划花费更多的成本和时间。
*   超出客户要求范围的过度设计的功能。

让事情走上正轨:会议的持续时间和坚持要求是至关重要的。

## 3 -货物邪教编程

[![alt](img/cb2a18a0a5336f9a30d3929aba4ea28b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ge5_mEcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://polemicsreport.com/wp-content/uploads/2016/09/cult.jpg)

你加入过邪教吗？好玩吗？还是你大部分时间都不知道那里到底发生了什么？*(插入尴尬脸)*

嗯，这基本上是什么货物邪教编程。你刚刚听说了一项新技术、流程或工具，尽管你对此一无所知，但你只是想参与炒作。通常，这种情况发生在我们年轻时，但有时更有经验的开发人员会因为加入而感到内疚。你周围发生的一些症状:

*   仅仅因为技术“时髦”就使用它，而不评估实现它的成本
*   复制粘贴编程

## 2 -熔岩流

[![ALT](img/6c9c81ffe3c5d8def79945436bbba7cc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eV88aDMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.npr.org/assets/img/2018/05/07/gettyimages-955631046_wide-0861ad06ae15b220e5d142e708423687dbfe574b-s1000-c85.jpg)

你知道你家里的那个房间，那些乱七八糟的东西就住在那里。甚至是那堆在你房间里移动的衣服，放在你的衣柜里会更好？那些是你自己的个人熔岩流:)

熔岩流是在我们的项目中被转移的那段代码，只是因为我们不想去想它需要做的所有工作。你知道这是无用的，丑陋的和潜在的错误。但是从事这项工作的压力会让你忽略将项目剥离的必要性。有时它是你甚至没有写的代码，你可以 99%确定它没有在任何地方被使用。但是那 1%让你无法清理。

您在项目中遇到的一些症状有:

*   代码中有一部分没人喜欢去。
*   一些代码看起来很重要，并且“可能”用在其他地方。
*   你把你的一些代码称为“那部分*(插入以前同事的名字)*做的，它可能服务于一个目的，可能...*(尴尬的停顿)*

## 1 -复制粘贴编程

[![Alt](img/af0b297931e198f9f6d0d234dace8fb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LYY9gltO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://effectivesoftwaredesign.files.wordpress.com/2016/05/copying_and_pasting.jpg%3Fw%3D640)

我们都曾经这样做过(现在仍然这样)，我并不是说从其他来源复制和粘贴是错误的。代码重用是我们行业的一个主要话题和基础。我们不能每次在应用程序中添加新功能时都重新发明轮子。世界很大，很多事情已经被尝试、测试和完成，所以为什么不重用现有的东西呢？

当我们做过头，或者不加思考地去做时，问题就出现了。复制和粘贴解决方案，而不分析它们在我们的代码或项目的整体架构中的影响，必然会导致许多令人头疼的问题。在您的项目中发生这种情况的一些症状有:

*   你看到同一个错误不断重复，即使你已经在别的地方修复了它。
*   你的代码开始看起来像一片拼凑的东西。
*   你的代码的某些部分变得晦涩难懂，因为你实际上不知道它们是如何工作的。

快速修复而不是代码质量的文化会刺激这种反模式。解决这个问题的一个简单方法是创造一种代码重用的文化，这种文化鼓励分析而不是速度。

这里的大多数模式都有深入的讨论[这里](https://www.amazon.com/AntiPatterns-Refactoring-Software-Architectures-Projects/dp/0471197130)
就这样，我希望你们喜欢这本书:)