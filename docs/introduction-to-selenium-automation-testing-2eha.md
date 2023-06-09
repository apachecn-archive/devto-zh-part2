# Selenium 自动化测试简介

> 原文：<https://dev.to/jessicaschuller/introduction-to-selenium-automation-testing-2eha>

在一个高度交互式和响应性软件过程的时代，许多企业都在使用某种形式的敏捷方法，自动化测试对于许多软件项目来说已经变得至关重要。自动化测试总是胜过手工测试，因为它需要的时间更少，人力资源出错的风险更低，允许定期执行，支持关闭执行，回归测试以及功能测试。有许多商业和开源工具可以用来支持自动化测试的发展。具体来说，Selenium 是为 web 应用程序构建自动化测试的最广泛使用的工具之一。

## 1.什么是硒检测？

[![Selenium introduction](img/93d5b31168d0f5184c48878dff70a97b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a-I5xRvN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Cross-browser-testing-in-Selenium-Webdriver-2.png)

Selenium 最初出现在 2004 年，当时 Jason Huggins 正在 ThoughtWorks 测试一个内部应用程序。意识到对他们的应用程序进行重复的手动测试变得无效，他创建了一个 Javascript 来驱动与页面的交互，允许他在各种浏览器上自动重新运行测试。Selenium 在当时是一项杰出的创新，因为没有其他产品允许用户用自己选择的语言来控制浏览器。

总的来说，Selenium 是一个开源框架，允许用户跨各种浏览器和平台测试应用程序。它提供了许多工具和 API，用于在 Firefox、IE、Google Chrome、Safari 等浏览器中实现纯 HTML 和 JavaScript 应用程序的自动化用户交互。使 Selenium 高度灵活的是绑定到不同通用语言的能力，允许用户用他们喜欢的编程语言编写，如 Java、Javascript、C#、PHP、Perl、Ruby、。Net 和 Python。

## 2.硒为什么会流行？

[![Test Automation](img/af4c875670e141a8a5baf2dc98694702.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3s9g0afk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Test-Automation.jpg)

目前的技术完全由机器主导，机器的行为由软件控制。这些机器会如我们所希望的那样运转吗？到处都是？每次都这样？软件测试是获得这些问题答案的关键。最终，软件应用的成功率将会控制你的业务发展。对于 web 应用程序来说也是如此，因为互联网目前是地球村的广场，网站已经成为为企业做广告的广告牌。

我们以电商行业为例。无论是亚马逊、Flipkart 还是易贝，在线公司都依赖网站的客户流量和基于网络的移动应用来开展业务。想象一下，如果发生了灾难性的事情，比如许多产品的价格被限制在 10 美元，而这一切都是因为代码中“不那么容易阅读”的一个小错误。我们如何防止这样的灾难？通过在部署前测试代码，对吗？这将我们带到手动测试的下一个主题。

手动测试是指 QA 测试人员手动测试 web 应用程序。测试需要在每个环境中手动执行，使用各种数据集，同时记录每个事务的成功率或失败率。依赖人类测试人员的挑战包括疲劳、厌倦、工作延迟、由于人工工作导致的错误和失误。这些缺陷表明了自动化测试的必要性以及 Selenium 的重要性。

## 3.分解硒系列

### 3.1.开始-硒 IDE

Selenium 集成开发环境(IDE)是 Firefox 插件工具，允许用户记录、回放、编辑和调试测试脚本，以便用首选的编程语言导出它们。由于测试域提供了特定的语言 IDE，用户可以使用该工具进行测试，而无需学习新的脚本语言，这使得向浏览器发送命令和自动化测试变得更加容易和方便。然而，用户只能使用该工具来测试 Firefox 中的 web 应用程序，因此不能使用 IDE 来测试其他浏览器。

### 3.2.进化——Selenium RC 和 Webdriver

Selenium Remote Control (RC)或 Selenium 1 可以帮助用户在各种浏览器中运行测试脚本，方法是将它们作为代理服务器启动。但是随着扩展版本- [Webdriver (Selenium 2)](https://seleniumtestingtutorials.com/selenium-tutorials/write-script-selenium-webdriver-java/) 的推出，不再支持 RC。

Webdriver 在开发人员和 QA 团队中广泛用于回归测试和跨浏览器测试，因为它允许用户使用本机操作系统功能在一系列浏览器和操作系统上进行测试。这意味着如果使用相同的浏览器，命令会像人一样执行。

### 3.3.优化-硒网格

Selenium Grid 允许用户在不同的浏览器和机器上同时运行多个测试，以加快测试速度。Selenium Grid 包含一个称为 hub 的机器和多个用于执行的节点。用户拥有的节点越多，他们可以同时运行的测试就越多。

[![Selenium IDE](img/0f000b34b57215378343318ccfc3ebe3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uCkmIoSx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Globel-networking-Converted-1024x662-1.png)

## 4.硒的未来

[![Selenium components](img/6d13ffadc825d39fa321b55017fffd5c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7HyuvNQo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/Components-of-Selenium-e1539747834705-1.png)

全球企业越来越倾向于采用 Selenium，因为自动化测试工具提供了诱人的好处——成本效益、高速、兼容性等等。这意味着在不久的将来对硒的需求将会大幅增长。因此，Selenium 4 将于 2018 年底发布。即将到来的 Selenium 版本 4 的主要改进已经公布，包括新的协议，新的插件系统和 CLI 运行程序，改进的 Selenium Grid 等等。

此外，基于 Selenium 的自动化工具越来越受欢迎。例如，Winium 是一个基于 Selenium 的工具，用于在 Windows 桌面上测试和自动化桌面应用程序。对于那些熟悉并已经知道 Selenium 上大多数功能的人来说，这很容易适应。Winium 目前有一些 bug，它仍在经历一个开发过程，就像 Selenium 曾经做过的那样。

[![Future Selenium](img/0b41d6c76ea83a4877425775fa52dcdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BD1C6z7h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://seleniumtestingtutorials.com/wp-content/uploads/2018/10/5-best-practices-of-implementing-selenium-test-automation-1.jpg)

此外，尽管 Selenium 是最流行的 QA 测试方法，但它需要编程技能、知识、时间和精力来创建测试自动化框架，以满足特定的测试需求。因此，越来越需要一种替代方法来消除 Selenium 的技术复杂性，以允许测试人员有效地设置、创建、运行、报告和管理他们的自动化测试。卡塔龙工作室(免费工具)、UFT(商业工具)、TestComplete(商业工具)似乎是这个部门中有价值的竞争者。

所以这个关于[什么是硒测试](https://seleniumtestingtutorials.com/selenium-basics/selenium-introduction/)的博客得出了这个结论。在软件测试领域，Selenium 已经成为一个无处不在的名字。随着自动化测试的不断发展，Selenium 和它的替代产品将会汲取它们的灵感，并和伟大的继承者一起继续帮助自动化测试变得更好。