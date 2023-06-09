# 运行生产系统:第 1 级，软件灭火

> 原文：<https://dev.to/backendandbbq/running-production-systems-level-1-software-firefighting-20j4>

*最初发表于我的博客:* [你所需要的只是后端](http://allyouneedisbackend.com/blog/2018/09/21/software-firefighting-running-production-software/)。

[![Software Firefighting](img/d273633b76216d61f7ed19325e987fba.png "Software Firefighting")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0W7Dsr8m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlrx4ptwkk57rf8sq428.jpg)

你建造它，你经营它。这个口号传遍了世界各地的软件工程团队。它工作得很好——成功的团队不仅关心编写好的代码，还关心代码如何服务于生产环境中的最终用户。

对于高负载的项目，运行软件变成了一个独立的学科，叫做“T2”站点可靠性工程。正如我的一个同事(前 DevOps 工程师，现在 SRE)告诉我的:

> #### SRE is the next level of DevOps

我认为工程团队应该知道在项目的最开始，软件将如何在生产中运行。它包括关于基础设施、数据存储、监控和部署管道的知识。很好，如果你知道所有东西的预算。

在*系列博客文章*中，我们关注我在职业生涯中见过的方法，从最简单的到最复杂的。

让我们来看看我称之为**软件救火**的第一层。

## 为什么要在你的代码中加入灭火泡沫

**软件救火**——是工程师们通常开始的地方。你不需要有任何经验来开始。这样做实际上可以帮助你成长...直到某一刻。

您编写一些代码，将其推向生产，并希望一切正常。是的，你没有太多关于系统如何运作以及健康程度的信息。

如果您的服务不能正常运行，这种策略适用于没有什么大不了的情况:

*   宠物项目
*   学生项目
*   黑客马拉松(我从来没有在黑客马拉松期间进行过适当的监控，你呢？)
*   原型/演示项目

但是我曾经在与业务相关的应用程序没有任何遥测技术的环境中工作过。

> #### My boss called me at 11pm and told me that the ~~sh * t~~ magic software couldn't work. The next morning we were demonstrating the system for our customers, who should pay for it.

相信我；一点都不好笑。在这样的调用之后，您通过 SSH 连接到运行该软件的机器。你试着去理解发生了什么。在软件灭火模式中，您尝试在生产中重现问题，以便在日志中看到有用的东西。如果您的日志不是很详细，或者您想跳到较低的级别，另一个选择是为正在运行的进程附加一个调试器。

如果你坚持用软件救火的方法来运行生产软件，我认为你很有可能会工作到深夜。深夜...对于夜间编码的爱好者来说，这可能是好的。但不确定你是否有加班费。

> #### The stall owner in another mainland dialed me. They asked to solve the problem of our software as soon as possible during the exhibition. It happened to me at 3 am. Exciting experiences that I want to avoid in the future.

当你找到原因时——你在真实的实例上进行实验，尝试修补代码，最终创建一个以单词`hotfix`开始的 PR，并在没有适当的同行评审的情况下将其直接部署到生产中。你的团队稍后会了解到这一点...希望不是新事故造成的。

## 终极软件救火工作流程

我警告过你，它不适合商业应用。现在我就如何做分享一下我的想法。

1.  找出问题所在。
    *   连接到生产环境。
    *   收集实时统计数据(下一节将讨论如何在该领域接受培训)。
2.  重现问题。
    *   本地化-找到它发生的地方。本地化区域越小——对你越好:服务、模块、类、方法、代码语句、变量...
    *   重复有害的行为，以验证这是否是正确的地方(只有从商业角度来看是安全的)。
3.  准备修补程序。
    *   在您的事件注册表/关闭的吉拉票证或堆栈溢出中找到类似的问题。
    *   在本地进行更改。
    *   在不接触生产的情况下进行测试。
    *   如果您确定要进行更改，请将它转移到生产环境中。
    *   验证问题是否已解决(并且没有产生新问题)。
4.  为你的团队写一篇博客，分享你从消防会议中学到的东西。

## 训练消防员

生产中的故障排除是一项非常重要的技能。我希望我有比现在更高的水平(但我不希望任何企业为此买单)。以下是故障诊断的级别，从最简单到较复杂的顺序排列:

1.  **检查运行应用程序的机器的状态(健康与否)**。

    *   **[top](https://linux.die.net/man/1/top)** -该命令收集您机器上的资源使用统计信息，并提供资源利用的动态视图。这是你能够获得一些情境意识的最简单的事情。学习链接:
        *   [Linux 平均负载:解开谜团](http://www.brendangregg.com/blog/2017-08-08/linux-load-averages.html)
        *   [理解顶部命令的输出](https://gtacknowledge.extremenetworks.com/articles/How_To/Understanding-the-output-of-the-TOP-command)
        *   [15 个实用的 Linux 顶级命令示例](https://www.thegeekstuff.com/2010/01/15-practical-unix-linux-top-command-examples/)
2.  **检查软件正在记录的语句**。您的服务、web 服务器、负载平衡器的日志——所有的东西。`grep`和`tail`是你在那里最好的朋友。

3.  **在不重启的情况下调查可疑的运行进程。**由于如何在产品中重现 bug 并不总是那么简单，当我得知我们可以连接到已经为我们的客户服务的代码时，我激动不已。它能让你更深入地了解细节。一些工具可以给你一些提示:

*   **[gdb](https://www.gnu.org/software/gdb/)** -是 GNU 调试器，允许你设置断点和暂时停止进程的执行，以实时查看变量值。你越了解程序的源代码，就越容易发现错误。当你需要调查一个崩溃时，你可以在程序崩溃前设置一个断点。一些提示:

    *   你可以使用 gdb 查看等待线程
    *   你可以使用 Python 来扩展 gdb(你也可以使用 GoLang AFAIK)

> #### This tool helped me solve a regular expression problem, which led to a major accident in a large cloud system.

gdb 给你更多的洞察力，这是你只能在日志中看到的。您可以找到许多关于如何为您的技术栈使用调试器的博客。我想分享一篇博文的链接，这篇博文是关于[Roman Podoliaka](http://podoliaka.org/2016/04/10/debugging-cpython-gdb/)用 gdb 调试 CPython 进程的。对于用 Python 编写的后端应用程序，使用 [pdb](https://docs.python.org/3/library/pdb.html) 会更方便。这里有[如何开始使用工具的伟大教程](https://github.com/spiside/pdb-tutorial)。

1.  **追踪盒子上的所有东西(网络流量，库调用，系统调用)**。如果以上都没有帮助的话——看看用于自省你的软件的工具和方法的宇宙。查看学习链接:

*   [选择 Linux 追踪器(2015)](http://www.brendangregg.com/blog/2015-07-08/choosing-a-linux-tracer.html)
*   [Linux 性能](http://www.brendangregg.com/linuxperf.html)

此外，我们还有几个用户推荐的工具([卡洛斯·内拉](https://twitter.com/CarlosN26157061)和[【王】](https://twitter.com/Amie42))来自[的相关 Twitter 帖子](https://twitter.com/BackendAndBBQ/status/1026138692514205699):

*   [BPF 编译器集合-工具](https://github.com/iovisor/bcc)
*   [kgdb](https://www.kernel.org/doc/html/v4.15/dev-tools/kgdb.html)
*   [用于 Solaris 操作系统的 mdb](https://docs.oracle.com/cd/E18752_01/html/816-5041/intro-1.html)
*   [Valgrind](http://valgrind.org/)

如果你对性能优化特别感兴趣，我强烈推荐你观看由[布兰登·格雷格](http://www.brendangregg.com/) - [制作的关于 **Linux 性能工具**的视频(90 分钟)第一部分](https://www.youtube.com/watch?v=FJW8nGV4jxY)和第二部分。他解释了[性能优化方法](http://www.brendangregg.com/methodology.html)，并提供了大量实际例子。还有[看他的博客](http://www.brendangregg.com/overview.html)。这是很大的学问。

在学习这个话题的过程中，我还发现来自[西奥·施洛斯纳格](https://lethargy.org/~jesus/page/about/)的幻灯片[很有趣。](http://lethargy.org/~jesus/misc/production-troubleshooting.pdf)

## 利弊

让我们分析一下牛仔风格运行生产软件的好处。

**优点:**

*   **英雄主义。**你觉得自己像一个从灾难中拯救企业的英雄。
*   **便宜。**不需要对基础设施进行投资。

**缺点:**

*   **影响您企业的声誉。**只有当客户/企业主发现服务不起作用时，你才会意识到。例如:你从 Twitter feed 了解到，你的服务对最终用户不起作用，他们正转向竞争对手。糟透了。
*   需要对工程团队进行培训。
*   **耗时。**您需要在 prod 中重现该问题，以收集机器上的遥测数据。
*   **不负责任的。**导致在生产环境中运行您的存储库中可能不存在的修补程序。团队中的其他工程师可能对如何解决这些问题一无所知。
*   **紧张的。**对你的身心健康有危险。以及你的个人生活。
*   **不合作。**如果需要步出，很难交接工作。

再次强调，我不建议在救火模式下运行商业软件应用程序。

我们可以消除这种方法的一些缺点。为了实现这一目标，我们需要成长并达到更高的成熟度。该系列的第二篇博文即将发表。

你最喜欢的调试/性能工具是什么？

这篇博客文章最初是由[的 Twitter 帖子](https://twitter.com/BackendAndBBQ/status/1026138692514205699)发布的。您可以订阅我的 Twitter 帐户或博客，不要错过下一次关于后端软件工程的知识分享会议。