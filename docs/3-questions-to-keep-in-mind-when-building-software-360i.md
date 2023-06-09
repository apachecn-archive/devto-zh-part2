# 构建软件时要记住的 3 个问题

> 原文：<https://dev.to/tability/3-questions-to-keep-in-mind-when-building-software-360i>

软件开发中有一个有趣的悖论。如果你把它归结到本质上，一个程序就是一组二元决策。事情不是真的就是假的。但在现实中，构建信息系统很少涉及对错。

这是因为大多数应用程序在不断变化的环境中运行。如果你正在创业，帮助你获得牵引力的 MVP 可能不适合大规模使用。如果你是一个市场，当你想扩展到其他地区时，对某一货币有效的支付流程可能会突然失效。如果你是一个微博平台，适合 100，000 人的适度功能可能不会吸引 1 亿用户。

除非你是在为一个变化极小的封闭系统写代码(视频游戏曾经是这样，但现在没那么多了)，否则你写的东西在未来极有可能是错的。这是技术和市场快速变化的自然结果。因此，编写软件最终是要考虑我们当前的情况和我们看到的趋势，做出权衡。在这里，我想提出三个问题来帮助你做出选择，不管你想解决的问题是什么，也不管你使用的是什么技术。

## 这给了我们多少带宽？

带宽的问题并不令人惊讶，当我们说一个解决方案是对还是错时，这往往是我们关注的焦点。但是这里的想法是将事情推进到更远，并尝试给出实现可能中断的粗略估计。

这不需要花费数周的时间进行昂贵的计算，就能精确地知道您的系统将开始出现故障的用户或文件的数量。但是，对您正在构建的容量有一个大致的了解是很重要的。有时候这很简单——我曾经在一个系统中工作，在这个系统中，年份是用文字写在代码中的，时间会超过 2010 年——有时候一个大概的数字就足够了。请记住，在这个阶段没有正确或错误的答案。你只需要对你认为你正在*购买*的时间设定一些期望值。

## 实现这个的成本是多少？

如前一段所述，您需要将交付解决方案的行为视为金融交易。你在争取时间，直到市场迫使你调整或改变你的产品。因此，您需要评估实现的成本，以便更好地理解其价值。

这里没有灵丹妙药，因为它需要与你的实际位置相平衡。如果你正在创业，你可以选择一个微服务架构，它会马上给你几年的带宽。但这可能需要你几个月的时间才能做好。因此，如果你仍在验证你的想法，那么花一周时间建造一个将在一个月内坏掉的原型可能就足够了。但是，如果每天已经有成千上万的人来使用您的服务，并且有几十个现有的功能需要支持，那么您需要在您的下一个功能上进行足够的投资，以便您暂时不必重构它。

## 更换的费用会是多少？

第三个也是最后一个问题是关于预期不可避免的重写你的功能、服务或产品。微服务如今如此吸引人的一个原因是，他们试图通过设计使系统易于改变来解决这个问题。然而，你仍然可以[通过一些良好的质量实践和隔离代码中的组件来为重构](https://blog.squadlytics.com/managing-technical-debt/)做准备。例如，我非常支持从后端 API 开始，因为它迫使你定义你的应用程序的边界，并且在将来更容易改变你的应用程序的部分(你只需要保持你的端点的输入和输出)。

更大的情况是，你需要将重构的成本加入到实现的成本中。最显著的好处不是它会让你改变你的架构。这将迫使你思考未来你需要什么来理解如何改进你的解决方案。这将导致更干净的代码，更好的文档。这将更容易理解拥有自动化测试的好处。这将有助于看到匆忙推出新功能的危险，即使它现在还能工作。

## 结论

显然，代码可能是完全错误的。但是我建议人们尽可能在规格、设计、代码审查和讨论中使用这三个问题。避开“这是不正确的”这一断言，谈论权衡将有助于促进沟通，并为团队和客户培养更健康的辩论。

Squadlytics 是一个面向软件团队的生产力分析平台。[今天就免费试用](https://squadlytics.com)。

(照片由 Unsplash 上的 [NeONBRAND](https://unsplash.com/photos/-Cmz06-0btw) 拍摄)