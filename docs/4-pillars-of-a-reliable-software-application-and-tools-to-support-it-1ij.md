# 可靠软件应用的 4 大支柱和支持它的工具

> 原文：<https://dev.to/geshan/4-pillars-of-a-reliable-software-application-and-tools-to-support-it-1ij>

软件开发不仅仅是在键盘上敲一些代码来得到想要的输出。软件开发的业务方面总是优先于 it 的技术方面。

我见过一些软件开发团队很慢，因为他们有很多技术债务。有些团队很天真，成员不想探索新事物。他们还停留在 10 年前的技术上，如 FTP、旧的遗留框架等。在这篇文章中，我将阐述一个可靠的软件应用程序的四个基本要素。这篇文章的内容是语言和框架不可知的。它适用于任何软件项目。

[![4 pillars of a reliable software application and tools to support it](img/401a114ac43aea554fb6b7d04e4de06b.png "4 pillars of a reliable software application and tools to support it")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BYZh2dm9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.img/4-pillars-software-application/4-pillars-software-application.png)

**假设**

对于这篇文章，我做了以下假设:

*   软件开发团队正在使用一些版本控制系统(VCS ),比如 git。
*   存在某种形式的项目管理和团队结构。

## 可维护代码

让软件工作超级重要，让它只是工作是个问题。我见过团队为了让代码工作而写代码。有时感觉代码只是为编译器/解释器而存在，而不是人类。代码，简单，可读和可维护性也是必不可少的。我们已经讨论了一些使代码可维护的规则。[把东西命名好](http://www.slideshare.net/pirhilton/how-to-name-things-the-hardest-problem-in-programming)写相关代码注释也是破坏。

拥有代码审查文化总是有帮助的。如果其他人可以在代码合并到主分支之前检查您的代码，这是一个很好的实践。它为学习如何实际编码开辟了更多的途径。代码审查最好在像 Github、T2、比特桶和 T4 这样的系统中进行。

## 持续集成的自动化测试

拥有自动化测试是必要的。选择与团队当前技能相匹配的测试类型。拥有单元测试是件好事。编写可测试的代码并给予团队成员进行单元测试的技能是一项投资。接近单元测试的最好方法是抓住低垂的果实。

最好从冒烟测试或必要的功能测试开始。在每次推送/部署时做一个 web 应用程序？检查所有页面是否响应 200 HTTP 响应。

逐步增加更多的测试和时间，让你的团队成员获得更多的技能。一旦有了某种形式的自动化测试，就用[持续集成(CI)](https://en.wikipedia.org/wiki/Continuous_integration) 服务将它连接起来。一些不错的选择是[特拉维斯 CI](https://travis-ci.com) 、[韦克尔](http://wercker.com)、[可发货 CI](http://shippable.com) 。

## 自动化部署

一个完成的特性应该在更短的时间内从开发人员的机器上投入生产。使用最新工具的自动化部署将成为可能。2018 年你用的是文件传输协议(FTP)吗？你应该马上停止，FTP 用户自动化的第一步是使用 [GitFTP](http://git-ftp.github.io/git-ftp/) 。与通过 FTP 客户端复制文件相比，这是一种将更改后的文件自动上传到服务器的方式。

只需一步部署，最少的人工工作。自动化部署流程使用 [Capistrano](http://capistranorb.com/) 、 [Fabric](http://www.fabfile.org/) 、 [Rocketeer](http://rocketeer.autopergamene.eu/) 等工具。综上所述，[最好的部署工具是...符合你需求的](http://geshan.com.np/blog/2015/08/the-best-automated-deployment-tool-the-one-that-fits-your-needs/)。2018 年，不如走 [Docker](https://docker.com/) 和 [Kubernetes](https://kubernetes.io/) 的路子。

## 日志记录与监控

日志是软件应用程序与您交流的唯一方式。收集您的所有系统、应用程序日志，并通过 web 界面进行访问。遵循日志记录[标准](https://tools.ietf.org/html/rfc5424)并使用免费的 SAAS 以可读的方式呈现您的日志，为您带来价值。

> 如果狗是人类最好的朋友，那么木头就是开发者最好的朋友。

理解并利用[登录软件应用](http://geshan.com.np/blog/2015/08/importance-of-logging-in-your-applications/)的重要性。

仅仅有日志是不够的，每次修改后都要查看日志。每次部署后都要检查日志，以跟踪任何可疑活动。您可以使用 [Sematext Logsense](https://sematext.com/logsene/) 、 [Logentries](http://logentries.com) 或 [Loggly](http://loggly.com) 进行云日志记录。对于应用监控[而言，New Relic](https://newrelic.com) 和 [AppDynamics](https://www.appdynamics.com/) 脱颖而出。

## 结论

我没有关注软件应用程序和部署的服务器和 DevOps 部分。这些是所有软件工程师都应该坚持的实践。这些应该是新项目开始时的目标。

> 没有 4 条稳定的腿，椅子也会倒下。遵循以下 4 个实践，避免您的应用程序崩溃。

* * *

最初发表于[Geshan.com.np](https://geshan.com.np)，你可以在那里读到更多的东西。