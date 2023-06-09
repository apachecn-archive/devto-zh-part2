# 测试和调试的区别

> 原文：<https://dev.to/executeautotest/the-differences-between-testing-and-debugging-104f>

[![The Differences Between Testing and Debugging](img/fc0cb03338c189ef07766ffd0c7094c2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VZWfH7m0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/testautomationresources.com/wp-content/uploads/2018/04/Devlopers-Vs-Testers-01.png%3Fresize%3D1024%252C387%26ssl%3D1)

当谈到软件测试时，由于完善产品定义的不同方法，测试人员和开发人员之间的战斗永无止境。测试和调试成了在这场无休止的战斗中使用的“武器”。但实际上，这些术语通常被误认为是相同的。

为了让你更深入地了解它们之间的区别，在本文中，我们将讨论测试和调试之间的差异，以及一些可以帮助你更有效地调试 T2 的技巧。

## 1。测试的区别&调试

什么是测试？
测试:基本上，测试是一个探索系统以发现软件中存在的缺陷的过程，不仅如此，这个过程还必须定位缺陷并定义一旦这些缺陷出现将会发生什么。这个过程由测试团队在测试阶段执行，在这个阶段之后，他们将向开发团队报告进行调试。

*一些流行的测试工具* : [硒](https://docs.seleniumhq.org/)，[卡塔龙工作室](https://www.katalon.com/)， [TestComplete](https://smartbear.com/product/testcomplete/overview/) ，…

**什么是调试？**
调试:一旦开发团队收到测试团队的报告，他们将开始调试。这一阶段的目的是找出软件中的缺陷并将其清除。这是一个一次性的过程，是手动完成的。在这个过程中，一种叫做调试器的特殊工具被用来定位错误，大多数编程环境都有调试器。

*一些流行的调试器工具* : [WinDbg](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/debugger-download-tools) ， [OllyDbg](https://en.wikipedia.org/wiki/OllyDbg) ， [IDA Pro](https://www.hex-rays.com/products/ida/) ，…

| 测试 | 排除故障 |
| --- | --- |
| 由测试人员执行 | 由开发人员或开发团队执行 |
| 可以手动或自动完成 | 只能手动完成 |
| 可以在开始测试时预先定义。测试结果是可以预测的 | 从未知条件开始，很难预测结果 |
| 找到编程故障 | 证明这只是一个无人注意的小错误 |
| 可以通过使用自动化测试工具自动完成 | 软件的自动调试仍然是程序员的梦想 |
| 目的是找到 bug | 目的是找到错误的原因 |

[![Typical Testing & Debugging workflow](img/bf76fe34079863eab9e1c5fe4389a4d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s----iOd4O1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/testautomationresources.com/wp-content/uploads/2018/04/Testing-debugging-workflow.png%3Fw%3D564%26ssl%3D1)

阅读更多:[分三步执行自动化测试](https://testautomationresources.com/web-testing/execute-automation-testing-in-3-steps/)

## 2。帮助您更有效地调试程序的 5 个技巧

调试时你首先要考虑的是用户体验。具体来说，如果你的软件性能很差，那么你的用户就会离开你。确定 bug 的优先级有助于了解 bug 对用户的影响有多大，并确定首先修复哪个 bug。如果可以使用风险评估矩阵来确定 bug 的优先级。

**-重现 bug**
在做任何改动之前，确保你已经重现了原来的 bug。如果您不重现错误并继续进行更改，那么如果有人因为错误尚未消除而重新开罚单，将会占用您大量的时间。你不会记得你以前做过什么。所以，一定要重现最初的错误，万一你不能重现，就去问能重现的人。

**——不要假设事情按照它们本来的方式运行**
修复 bug 时需要一定程度的偏执。很明显，有些事情并没有按照预期的那样运行，否则，你就不会面临问题。对问题可能出在哪里持开放态度，同时牢记你对相关系统的了解。您不太可能(但有可能)发现问题的原因是一个常用的系统类——如果它看起来像 system 的话。String 行为不当，您应该在声称已经发现一个明确的 bug 之前对照文档仔细测试您的假设，但是问题总是有可能是外部的。

**-知道错误码**
知道 HTTP 错误码会给你诊断 bug 带来很大的优势，比如:

404–您的应用程序中可能有错误的 URL
401–您的凭据可能是错误的
418–您正在与茶壶对话！
429–你的请求太多了
如果你得到一个 HTTP 错误代码，总是谷歌一下以确保你理解它。再说一遍，这会节省你很多时间！数据库驱动和其他协议也是如此，用数据库的名称搜索错误，并查找官方文档。

**——如果完全“卡住”，寻求帮助！**
如果你不知道如何解决你面临的问题，感到非常沮丧，这里有一些解决方法:

*   休息一下，从一个不同的角度接近臭虫。
*   更加积极地进行跟踪和记录。
*   搜索引擎、论坛……是你从同龄人、社区甚至专业人士那里找到问题答案的强大途径。给他们尽可能多的信息，他们会帮你搞清楚。
*   破坏点东西(只是为了减压！)

**结论**
归根结底，测试和调试的存在只有一个原因，那就是让产品越来越好。无论你属于哪一个团队(测试团队或调试团队)，当产品的改进得到积极的反馈时，你都是快乐的，这也是战斗暂停的时候。

感谢您的阅读和愉快的测试！
原始来源:[测试和调试的区别](https://testautomationresources.com/software-testing-basics/differences-testing-debugging/)