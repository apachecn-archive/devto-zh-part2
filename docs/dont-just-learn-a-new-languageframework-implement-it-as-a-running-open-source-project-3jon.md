# 不要只是学习一门新的语言/框架，要把它作为一个正在运行的开源项目来实现

> 原文：<https://dev.to/geshan/dont-just-learn-a-new-languageframework-implement-it-as-a-running-open-source-project-3jon>

“对于我们在做之前必须学习的东西，我们通过做来学习。”――亚里士多德，《尼各马科伦理学》。想象你已经读了 3 本关于骑自行车的书。然后有人给你一个周期，要求骑，你会骑吗？简单的回答是“没有”。这不是关于你读了多少关于骑自行车的书或者看了多少关于骑自行车的视频。这是关于骑自行车，保持平衡，学习脚，手和眼睛的协调。学习一项新的技术技能、一门新的语言或框架也是如此。

[![Cycle by doing not reading](img/fe50a55db8045995380742fc785e8bb0.png "Cycle by doing not reading")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--esGNEy4g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/language-framework-learn/cycle.jpg)

现在，如果你认为我将如何做这个新项目，我甚至不知道语言“X”或框架“Y”停止抱怨。也许你是科技或编码领域的新手，但你需要知道如何学习新事物。学习新事物的最好方法就是去做。这篇文章将集中讨论一些常见的事情。它将帮助你从“我想学 X”到“我有一个项目在 X 上运行”,所以继续下去。如果你确信做一个项目是为了学习新的东西，那么就开源它。Github 是托管您的开源项目的事实上的服务。然后你可以免费使用很多服务。这篇文章对编码新手和有经验的软件工程师同样有用。

## TLDR；

> 写一个项目来学习语言/框架，开源它，然后免费利用服务。不要只是看课程，读文件，然后找到解决方案。边做边学。使用 git 并在项目中实现 docker。代码正确添加代码质量检查服务要了解最佳实践，请部署您的项目以获得它的工作 URL。

[![Learn a new thing by doing not reading](img/fe52188828aca74bdff811878938e50f.png "Learn by doing")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y3AQjYUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/language-framework-learn/lang-framework-learn.jpg)

## 不要只是看课程，阅读文档，寻找解决方案

如今，学习新事物的选择太多了。尽管如此，视频课程是最受欢迎的媒体之一。你可以在 [Udemy](https://www.udemy.com) 、 [Pluralsight](https://www.pluralsight.com/) 甚至 [Youtube](https://youtube.com) 上学习新的东西。除非你在实践中学习，否则看视频只会在一定程度上有所帮助。你最好查阅一下官方文件。例如，阅读 React JS 文档比仅仅学习 React JS 课程要好。你找出创作者的视角。创建另一个 Javascript 框架/库的原因是帮助您找到合适的解决方案。

## 学会 Git 协作

“没有人是一座孤岛”，尤其是在科技领域，你通常不会孤军奋战。你是团队的一员。所以，即使在学习新东西的时候，也要试着找一个可以合作的人。与任何竞争对手相比，Git 都非常受欢迎。当不止一个人为一个项目编写代码时，这是有益的。你应该边做边学 git，查看 Github [教程](https://try.github.io/)。我强烈推荐“边做边学”部分。在你把它推送到 Github 之后，任何人都有可能对它做出贡献。

## 实现 docker，搞定我的机器综合症

2018 年，如果你想让你的应用更易访问，使用 [Docker](https://www.docker.com/) 。这也将有助于增加你的开源项目的贡献。使用 docker compose 在本地运行您的项目就像执行两个命令一样。Docker 有很多优点。对于初学者来说，这是一种确保你的应用程序在你的机器，你朋友的机器上以相同的方式运行的方法。在您部署应用程序的服务器上也是如此。只要它在 Docker 上运行良好，您就可以放心，它在任何环境下都不会出现问题。

## 添加代码质量检查

只是让它工作不应该是你的优先事项。写作质量也应该是你优先考虑的。为您为学习而创建的开源项目添加代码质量检查。根据语言/框架，你可以选择任何服务。我强烈推荐[代码气候](https://codeclimate.com/quality/)。代码气候为移动开发者提供了从 Javascript 到 PHP，从 Java/Kotlin 到 Swift 的各种语言支持。有了新的[浏览器插件](https://codeclimate.com/browser-extension/)，你可以在 Github 的拉请求屏幕上看到你的代码。你只要把它连接到你的 Github repo 上一次，就可以开始看到你的代码质量报告。然后你也可以提高你的代码质量。你可以看看这个[的例子](https://codeclimate.com/github/geshan/currency-api/src/exchangeRates.js/source)。

## 部署您的项目

现在你在写一个新项目，学习你最喜欢的‘X’或者‘Y’。你已经写了它的一些部分，你正在与 Git 合作，并在 Github 上开源代码。你有 Docker，每次推送都有代码质量检查，太棒了！但是你不能给你住在不同城市/国家的朋友看，对吗？不对！

您可以使用不同的服务来部署 web 应用程序。有了网址，你可以把它展示给你的朋友、招聘人员或任何知道网址的人。你可以将它免费部署到像 Heroku 或 T2 Zeit Now 这样的服务上。
如果你已经将你的开源应用 Dockerized，我现在推荐 Zeit。随着最近的 [Github 集成](https://zeit.co/github)，Zeit 现在将为每个拉请求提供一个新的 URL。这使得测试变得轻而易举。你可以看看我写的一个货币转换器 API 的例子。

## 结论

总之，边做边学是学习新事物的最好方法。你的目标应该是不仅让它工作，而且按照最佳实践去做。这就是代码质量发挥作用的地方。如果你能添加自动化测试和持续集成，这将是初学者的锦上添花。祝你在实践中学习一切顺利！

* * *

最初发表于[Geshan.com.np](https://geshan.com.np)，你可以在那里读到更多的东西。