# 盎格鲁中心主义打破了我的测试——忽视本地化，后果自负！

> 原文：<https://dev.to/tdwright/anglocentrism-broke-my-tests--ignore-localisation-at-your-peril-4p5n>

最近在 ConTabs 项目上出现了一个问题，让我陷入了一个本地化兔子洞。你看，我已经写了一堆一致性测试，包括示例输出。我没有考虑的是有多少依赖于我的地区。这些可能会在具有不同区域设置的计算机上失败。在今天的帖子中，我想探究到底哪里出了问题，以及我们如何纠正它。

## 发生了什么？

几周前，我注意到一个事实，ConTabs 项目中的一致性测试对于一个用户来说[失败了。这些测试使用测试数据来运行表生成的整个端到端过程。他们将生成的表与硬编码的示例进行比较。例如，我们有测试表明一个简单的表应该是这样的:](https://github.com/tdwright/contabs/issues/55)

```
+--------------+-----------+----------------+
| StringColumn | IntColumn | CurrencyColumn |
+--------------+-----------+----------------+
| AAAA         | 999       | 19.95          |
+--------------+-----------+----------------+ 
```

看起来无害，对吗？电脑之间有什么不同？果然，这个测试已经愉快地通过了一年多，在我的笔记本电脑上，在我的竞争情报系统上，在那段时间里参与 ConTabs 的其他 7 个人的电脑上。

但是，当斯洛伐克开发商 Marek 加入我们时，一切都变糟了。我发现，斯洛伐克是许多不使用句号(我的美国朋友称之为“句号”)作为小数点分隔符的国家之一。

[![](img/6b82465471dbf341981a526015031048.png)T2】](https://i2.wp.com/blog.tdwright.co.uk/wp-content/uploads/2018/11/High-Tatras-Mountains-Pleso-Slovakia-3223538.jpg)

斯洛伐克美丽的塔特拉斯山脉。(图片由[最大像素](https://www.maxpixel.net/High-Tatras-Mountains-Pleso-Slovakia-3223538)提供。)

因为 ConTabs 使用。NET，我们可以免费获得本地化。这意味着运行在斯洛伐克(或秘鲁，或丹麦……)计算机上的 ConTabs 将数字显示为“19，95”。这似乎是正确的行为，但我们的测试失败了。

当我们的程序行为正确，但测试失败时，测试显然是错误的，需要重新检查。

## 侧栏:为什么还要写这些测试？

我知道有些人已经阅读了上面的内容，并认为这是进行测试的“错误方法”。一致性测试本质上是脆弱的，并且很难变得有用，所以许多项目根本不采用它。单元测试通常要容易得多，所以总体来说，人们会更喜欢它们。

对于 ConTabs 项目，我们使用一致性测试(与单元测试一起)的原因如下:

1.  ConTabs 输出的性质(总是一个字符串)及其可预测的行为使得编写一致性测试非常容易。
2.  对我们来说，一致性测试实际上是表达一些需求的最简单的方法。例如，我们可以测试当我们对列应用右对齐时，小数点分隔符是否正确对齐。
3.  我们希望避免单元测试通过，但是部分的总和不对的问题。请看[这篇文章](http://blog.tdwright.co.uk/2018/01/02/when-testing-misses-the-mark/)中的例子…
4.  以便测试可以作为规范和文档。这有点抽象，但是在测试中有预期输出的例子以及用来产生它们的代码是一个非常有用的方法，可以看出什么是(或者应该是！)继续。

所以我们开始吧。虽然这种测试通常不太适合，但对于 ConTabs 项目来说，这种测试工作得非常好。不管怎样，继续表演…

## 我们如何修复它(第 1 轮)

认识到我们需要考虑不同的方法来将整数和它们的分数朋友分开，我们开始考虑我们可以做的方法。一方面，我们可以使 exemplar 成为变量，并将当前地区的小数分隔符注入其中。我觉得这不太对劲。这些例子应该是静态的——特别是如果它们要履行其作为文件的作用。

另一种方法(也是我们选择的方法)是显式固定线程应该使用的文化。通过这种方式，我们可以确保一定程度的可移植性，无论我们身在何处，都可以随身携带一个知名的语言环境。大概是这样的:

```
Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture("en-GB"); 
```

现在，让我们用同样的方法来参数化我们的单元测试:

```
[TestCase("en-GB", "£1.91")] [TestCase("sk" , "£1,91")] public void CurrencyFieldCanBeFormatted(string culture, string expected) { // Arrange Thread.CurrentThread.CurrentCulture = CultureInfo.CreateSpecificCulture(culture); var tableObj = Table\<TestDataType\>.Create(); // Act tableObj.Columns[2].FormatString = "£0.00"; var val = tableObj.Columns[2].StringValForCol(1.911M); // Assert val.ShouldBe(expected); } 
```

不错！快速解决办法。啤酒时间到了。

## 日期呢？

啊，是的……我应该意识到还有更多。

我*已经*(部分地)预见到了世界各地使用的不同日期格式，通过 a) [在整个一致性测试中隐藏日期列](https://github.com/tdwright/contabs/blob/4beef82c05f52f5fc558432b5c2b833ec3a10d22/ConTabs.Tests/ConformanceTests.cs#L58)和 b) [在单元测试中明确提供日期样式](https://github.com/tdwright/contabs/blob/4beef82c05f52f5fc558432b5c2b833ec3a10d22/ConTabs.Tests/FormatTests.cs#L17-L21)。然而，由于我们现在对地区更加敏感，我们似乎应该测试日期是否被正确地本地化了。因此，我的斯洛伐克合作者明智地添加了[一个针对一些日期定位的参数化测试](https://github.com/tdwright/contabs/blob/21752db74c1839a8b77d93c20d9bcb80d2a3cc1c/ConTabs.Tests/FormatTests.cs#L12-L28)。

任务完成。我们已经解决了本地化问题。我合并了这些变化，我们继续我们的生活。

## 现在一切都变得奇怪了…

好吧，如果不是因为几天后试验破裂，我们会继续前进。具体来说，使用斯洛伐克本地化的测试用例开始失败，因为一些机器添加了以前没有的空间。例如，我们希望日期的格式是这样的:

```
31.1.2018 
```

但是现在我们看到它们被格式化为:

```
31\. 1\. 2018 
```

这是一个非常令人不快的惊喜。测试*一直在我的笔记本电脑上运行，但现在不行了。他们已经通过了 CI 系统和我的斯洛伐克合作者的电脑。但是现在他们失败了。*

困惑之余，我[向 Stackoverflow](https://stackoverflow.com/q/53127994/50151) 发了一个问题。多亏了一位名叫 Jimi 的用户的解释，我意识到语言环境不是我们可以依赖的固定不变的东西。一句中肯的话:

> Windows 更新可能会更改任何区域设置的默认模式(无需明确通知)。

鉴于测试仅在几天前才开始运行，我们似乎碰巧经历了斯洛伐克语言环境的变化。从某种意义上说，时机是偶然的。如果变化发生得很晚，测试在我们的脑海中就不会是新鲜的，突然的中断会更加令人困惑。

## 那么该怎么办呢？

好的，我们知道对任何本地化的预期输出进行硬编码会带来风险。我们无法控制如何或何时更改某个区域设置，甚至无法控制两台计算机之间的差异。那么，我们如何测试我们的程序正在正确地处理本地化呢？

我们的解决方案仍在努力，但在这个过程中，我发现考虑我们每个测试的目的是有帮助的:我们试图断言的是什么行为？

在 ConTabs 的例子中，我认为受本地化影响的测试应该分为两类:

**1。一致性测试**，它显示了系统作为一个整体的预期行为，正如一个例子所明确描述的。

在这种情况下，我们必须选择一个地点并接受风险。我假设一些地方会比其他地方更不稳定，并且这些地方应该是首选。我没有这方面的证据，但我的直觉告诉我，en-GB 或 fr-FR 等语言环境比 sk-SK 等不太知名的语言环境更不容易改变。

**2。单元测试**，断言用户的区域设置被适当地应用。

我们的程序尊重用户的本地化偏好是很重要的，但是我们实际上并不关心这些设置是什么。在这些情况下，我们真正关心的是呈现单元格内容的方法输出的内容与我们应用本地化时得到的内容相匹配。换句话说，我们可以将我们的方法与。NET 本身，并避免必须硬编码任何预期值。大概是这样的:

```
// arrange var refDate = new DateTime(2018, 01, 31); var culture = CultureInfo.CreateSpecificCulture(cultureName); Thread.CurrentThread.CurrentCulture = culture; var dateString = refDate.ToString("d",culture); // act var tableObj = Table\<TestDataType\>.Create(); tableObj.Columns[3].FormatString = "d"; var val = tableObj.Columns[3].StringValForCol(refDate); // assert val.ShouldBe(dateString); 
```

应该可以了！

## 包装完毕

本周我了解到本地化是一个棘手的问题。一方面，忽视事物本地化的事实是不安全的。另一方面，假设语言环境是静态的也不是一个好主意。区域设置应该被视为数据。这意味着测试它们可以被处理，同时(尽可能)避免测试它们的内容。

处理这个问题的过程也迫使我更加仔细地思考我们编写测试的目的。我们在测试什么？这次测试的失败会让我们能够修复某些东西吗？我认为，后一个问题的答案非常重要。我经常觉得自己倾向于编写涵盖所有情况的测试，因为事情是在当前的情况下，但是考虑超出个人责任的事情变化是很重要的。换句话说，在对受潜在易变本地化数据影响的方法的输出进行硬编码时，我们在单元测试中引入了外部依赖性。

当然，如果我是一个讲英语的英国人，这一切都不会发生。我的盎格鲁中心主义允许我悄悄地忽略其他人以不同方式做事的事实。在软件开发中，我们主要用英语做生意。我的意思是，想想你选择的语言的内置方法的名字——我打赌它们是英语单词。这让我们很容易忘记这个世界的存在，这在我看来有点可耻。

[![](img/339ef53fd951e4be797f5f87add8ab8a.png)T2】](https://i2.wp.com/blog.tdwright.co.uk/wp-content/uploads/2018/11/798px-Imperial_Federation_map_of_the_world_showing_the_extent_of_the_British_Empire_in_1886.jpg)

至少我的特权没有延伸到殖民……(图片[来自维基媒体](https://commons.wikimedia.org/wiki/File:Imperial_Federation,_map_of_the_world_showing_the_extent_of_the_British_Empire_in_1886.jpg))。)

因此，我真的很高兴开源项目的国际性让我认识了 Marek。他在一台电脑上运行了测试，这台电脑的语言环境与我的完全不同，因此我们解决了这些问题。虽然我们仍在制定最终修正的全部细节，但我认为这是一个非常好的结果。谢谢你，马芮克！