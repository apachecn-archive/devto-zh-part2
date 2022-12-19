# 从为什么开始培养行为

> 原文：<https://dev.to/weiji14/developing-behaviours-by-starting-with-why-4n78>

*最初发布于[letsjustwander . now . sh](https://letsjustwander.now.sh/developing-behaviours-by-starting-with-why/)T3】*

> 为了警告每个人灾难性的海平面上升，
> 作为一名科学家，
> 我想公开分享一张南极洲下面的更好的地图。

因此，我在最近的几篇文章中，一直在以一种类似禅[禅](https://en.wikipedia.org/wiki/Chan_Buddhism)的恍惚状态，漫无边际地谈论着**的突然性**。的确，人们真的[不应该一步一步来](https://letsjustwander.now.sh/three-box-carts-in-one)，因为[这都是头脑中的](https://letsjustwander.now.sh/throw-it-into-the-trash-icon)而且有[更好的方式来看待事物](https://letsjustwander.now.sh/time-as-a-whole-instead-of-sequentially)。

然而，改变不仅仅是关于空虚，它也是关于平衡。

因此，今天我们将后退一步，看看你可以采取的每个步骤，以达到圣杯。

请注意，这是我在过去两周左右经历的事情的高潮。有句话说，读博士就像经营自己的小公司。一个博士生就像是一个孕育着一个想法的最初火花的创始人，一个把它变成一个有形的东西的工作者，同时也是一个向人类出售好处的销售人员。

先说最大的问题——为什么？

## 为什么为什么为什么

在[黄金圈](https://www.ted.com/talks/simon_sinek_how_great_leaders_inspire_action)上观看这个精彩的视频:

[![IMAGE ALT TEXT](../Images/7f0d90266f88e9d57bd4eff4ab3a2dc7.png)T2】](https://www.youtube.com/watch?v=qp0HIF3SfI4)

在视频中，Simon Sinek 告诉我们从“为什么”开始他说，这是一个令人信服的论点，不是由心理学驱动，而是由生物学驱动。从“为什么”开始挖掘大脑的边缘部分，据说可以克服我们现代的新皮层。

然而，这不仅仅是一个商业问题，两周前，我参加了一个关于撰写有效博士论文的研讨会。其中一个要点是把所有好的部分放在句子的开头，放在摘要的开头，也就是你论文的前几章！不要把所有有趣的部分留在文章的最后，人们想知道**为什么**你的作品是原创的。

在学术界，你只需要浏览一篇论文的摘要和结论，就能得到提出的核心观点，这几乎是一个笑话。不过，你真的不想因为把所有精力都放在结论上而在最后一刻写摘要而失去一个读者。抓住观众，把你最好的一面展现出来——在开始的时候！

除了商业和写作，你还把这个思路应用到其他领域！在我有时写博客的个人理财世界里，你经常被告知要先提出一个目标。是的，在制定预算或找理财顾问之前，先设定一个目标！

但不是任何目标，你需要努力思考**为什么**你想要达到那个目标。你如何做的**仅仅是数学和勇气，而当你达到目标时，多巴胺激增的**是什么**背后的原因。**

 **你知道吗，甚至有一种形式化的方法可以把它写出来？当我开始创建单元测试时，我发现了这一点...

## 如何——用一种叫做小黄瓜的语言

[小黄瓜](https://en.wikipedia.org/wiki/Pickled_cucumber#Gherkin)——一种腌黄瓜。它通常用于让人们达成共识。嗯，[小黄瓜语言](https://en.wikipedia.org/wiki/Cucumber_(software)#Gherkin_language)确切地说，不是黄瓜。

作为一种特定领域的语言([被翻译成 70 多种语言](https://docs.cucumber.io/gherkin/reference/#spoken-languages))，小黄瓜被用于编写行为场景。具有直观语法的“特征”文件。这些。“特征”文件是非技术人员可以理解的编码模板，技术人员使用它来指导他们在[行为驱动开发](https://en.wikipedia.org/wiki/Behavior-driven_development)中的编码。参见 [BDD101](https://automationpanda.com/2017/01/25/bdd-101-introducing-bdd/) (面向 Python)。

这是我今天早上为我当前的项目想出的一个例子:

```
 # language: en
    Feature: High Resolution Bed Elevation Model of Antarctica
      In order to have a better map of Antarctica's bed
      As a glaciologist,
      We want a model that learns from many open datasets  

      Scenario: See high resolution bed
        Given low and high resolution images of a bed
         When the user selects some view of Antarctica
         Then a high resolution bed elevation map is returned 
```

Enter fullscreen mode Exit fullscreen mode

这个特征文件的精彩之处在于它是高级的**并且**阐明了需要做什么！

“功能:”基本上就是标题。其下是语境。你会看到我使用了类似黄金圈的东西，从 **Why** 开始。与黄金圈不同的是，我加入了下一个**是谁**，暗示了以用户为中心的设计的重要性。最后，**是什么**被拼写出来，它要么是一个模型，一个应用程序，一个奇特的功能，或者...你明白了。

“场景:”展示了**该功能如何工作。在它下面是一个给定的 When-Then 语句，一种通过例子进行[说明的形式。我认为它是一个输入-处理-输出的管道。*给定*一些数据或知识，*当*你面对一些事情(应用程序必须对此采取行动)，*然后*返回一个输出。](https://en.wikipedia.org/wiki/Specification_by_example)**

以此为自以为是的首发模板:

```
 # language: en
    Feature: 
      In order to ... <Why>
      As a ... <Who>
      I/We want ... <What>

      Scenario: <example>
        Given ... <input>
         When ... <something happens>
         Then ... <output> 
```

Enter fullscreen mode Exit fullscreen mode

如果想要更多的例子，我发现 Python[behavior 教程](https://behave.readthedocs.io/en/latest/tutorial.html#feature-files)是比较搞笑的一个，有忍者！它显示了除了最初在行为驱动开发中的应用之外，人们还可以在许多不同的方面使用它。为什么不为你想养成的新习惯制作几张卡片呢？

```
 # language: en
    Feature: Healthy Eating
      In order to live longer
      As a healthy person,
      I want to eat a balanced diet

      Scenario: Eating fruit
        Given a fruit bowl full of juicy fruit
         When the healthy person hasn't been any fruit eaten today
         Then there should be less fruit in the fruit bowl
          And some fruit in the healthy person's tummy 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我有点(ab)使用小黄瓜的目的不是为它设计的，也许打破了一些标准！去阅读[如何用正确的语法写出好的小黄瓜](https://automationpanda.com/2017/01/30/bdd-101-writing-good-gherkin/)(例如，链接建议用第三人称而不是第一人称写作)。或者也许跟随一些[好的小黄瓜例子](https://automationpanda.com/2017/01/27/bdd-101-gherkin-by-example/)。

我们现在已经经历了一种方法，用小黄瓜语言正式写下我们想要做的事情。那现在怎么办？

你说“这对我有什么好处”？

## 我有什么好处

对任何人来说，争论如何做事(实现)都很容易。但是争论为什么(原则)要困难得多。

从核心的**为什么**开始，你故意让事情变得尽可能脆弱/尴尬。如果这是一个强烈的为什么，它会留下来。如果不好，人们会立即意识到这一点，你可以在最根本的问题被一层又一层的主观问题掩盖之前纠正它。

**为什么**要尽可能说清楚。随着时间的推移，不同的人会对**的含义**和**如何**使用“为什么”中的信息有不同的解释。用例会改变，技术会改变，但是一个好的**为什么**应该经得起时间的考验。

例如，上周三我去参加了一个关于管理文档和数据的研讨会。

他们从备份的 **Why** 开始——这样你就不会在地震等突发事件中丢失你的工作。这是我在我的[地理多样化](https://letsjustwander.now.sh/geographical-diversification)帖子中提到的内容。

**的**是如何实现的。在这一点上，我们已经有了不同的看法。研讨会的人推荐了一个非常“可靠”的云存储提供商，而我更喜欢像 git 这样不依赖于提供商的东西。

**什么是**使用适当的备份解决方案的好处，比如让您安心、确保您的工作继续等。

当我写这一段的时候，我发表作品的地方(Github)正在经历一些[登录问题](https://status.github.com/messages/2018-10-22)，人们担心潜在的数据丢失。是的，这很烦人，但我不会太大惊小怪，因为我已经有了我的连续性计划。由于 git 的[可替换性](https://en.wikipedia.org/wiki/Fungibility)的性质，有了我工作的两个本地副本，如果需要的话，我可以将它同步到任何其他 git 提供者。

如果我用了大学里的人推荐的东西，而那个供应商失败了，或者如果我毕业了，被大学系统开除了...是的，我可能仍然有我的两个本地副本，但是我会失去我的版本控制，并且会花费大量的时间和精力去寻找另一个备份提供者。在那段时间里，谁知道你的个人电脑会发生什么？！有了可替换的 git，我可以简单地添加一个新的遥控器，然后像什么都没发生一样在一分钟内把它推上来。

从“**为什么**开始，你就知道了根本原因。“如何”做事情可以改变，“你的用户是谁”可以改变，“他们对你的想法/产品的看法”可以改变，但如果你有一个令人信服的“为什么”，这些差异就不重要了。

也许十年后，会有更好的解决方案，更好的做事方式。然而，让备份高枕无忧的**原因**不会真正改变。甚至有可能**新的解决方案提供了更全面、更简单的东西，这在今天的我们看来就像魔术一样。**

那么，谁会从中受益呢？这仅仅是为了把我们所有的语言都变成严格单调的小黄瓜式语法模板的宣传吗？

## 谁在乎

### 黑客

老实说，我是一个喜欢深入细节的好文章的人。一篇博客文章可能有一个**如何**-什么-为什么的结构，我不会眨一下眼睛。就像，告诉我它是怎么工作的！！也许开发者和工程师读到这里会有同感。

你可能会成为一名伟大的工程师、开发人员或黑客，为生活带来愿景。然而，有一种过度设计的可能性。你可以从 bug 驱动的开发到测试驱动的开发，再到行为驱动的开发，等等，但是这些都是必要的吗？你能设计出更简单、更容易维护的东西，让[覆盖 80%的用例](https://letsjustwander.now.sh/climbing-the-pareto-curve)吗？

### 测试员

奇怪的是，小黄瓜语法来自行为驱动开发(BDD)，它本身是测试驱动开发(TDD)的扩展。测试人员类似于用户，他们实际上是使用产品的人，发现**它做什么**。

你可以如此以用户为中心，以失去理智为代价，专注于你的客户。当然，顾客永远是对的，但这并不意味着顾客可以发号施令，违反法律来得到他们想要的东西！我在服务行业工作过，你应该知道有些特权顾客会觉得自己有权给你的一天制造混乱。他们可能对你的提议不满意，你需要接受这一点。

### 空想家

这难道不是 **Why** -first 语法看起来像是来自有议程的商业导向的人的一些推动吗？我们为什么要相信他们的愿景？当然，想出一个愿景，让我们都坚持下去，建立你的帝国，使用你的商品。

对于商人、教师或任何有灵感的领导者来说。当然，也许对你来说用(好的)小黄瓜写作是一个过分的要求。当你的跟班们忙着做这件事的时候，你很容易发一条推文或者引用一句俏皮话。我真的不知道该说什么。也许要小心你赋予这个世界的理想，因为[将它推向极端](https://letsjustwander.now.sh/on-falling-down-the-optimal-rabbit-hole)可能是危险的。

### 继续赶路！！！

我知道，很容易专注于自己的领域。虽然你有一些伟大的人可以详细地回答所有三个问题，或者三个问题中的两个(为什么-如何-什么)，但这是罕见的。

作为一个人，我发现很难平衡这三者。

回到我的博士学位...有几天或几周，我会全神贯注于编码，实现这个星球上很少有人做过的很酷的前沿东西。当谈到写作时，深入到“为什么”可能会很可怕问题，因为这意味着大量的灵魂探索。接下来，我也需要解释我自己，我在博士期间做了什么，这对我们的世界意味着什么！

正如我在开始时提到的，这都是关于平衡的。

为了达到这种平衡，你需要沟通。无论是团队成员之间的交流，还是与你大脑中做不同事情的不同部分的交流，我们都需要找出如何将这些点连接起来。

在一天结束的时候，你需要达成共识。先从**为什么**说起。希望你会发现小黄瓜规范有助于你的思想形式化。至于接下来会发生什么，就看你自己去发现了！

走吧。**