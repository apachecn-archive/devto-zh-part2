# 自动化的第一步

> 原文：<https://dev.to/thaiwood/your-first-step-in-automation-204g>

# 你自动化的第一步

先写下来。让你走向自动化的第一步是首先把它写下来。你可能认为你现在没有一个可以自动化的过程。但是除非今天是你第一天工作，否则你已经准备好了一些正在使用的流程。如果你有代码，一个应用程序，或者一个客户可以看到的生产中的网站，你有一个部署过程。无论您是否意识到，您都有一个部署管道。留出 30 分钟来完成这项活动。根据你的产品的复杂程度和你(或你的团队)对过程的熟悉程度，时间可能会更长或更短，但是 30 分钟通常足够给你一个想法。如果您的部署流程目前是由一篇文章驱动的，那么您仍然有一个流程。你*可以*从无自动化到简单自动化。你所要做的就是把它写下来。不要因为自己的流程一塌糊涂，就觉得自己不能有自动化。不要想“我还没到那一步”。你到达那里的方法是从现在开始。你写下了什么？你先问自己一些问题，然后写下答案。如果一切顺利，会发生什么？把它写下来。它可能看起来像这样:首先，一个开发人员将一些代码推到一个存储库中。代码在代码库中之后会发生什么？只关注理想的道路，不要担心除此之外的任何事情。有人把它复制到测试环境吗？他们建造它吗？写下来。当测试环境中的测试成功时会发生什么？它会去新的环境吗？写下来。那还要再检测一次吗？写下来。一旦成功了，会发生什么？最终会投入生产吗？也许它进入生产环境的方式是被复制到服务器上，服务被重新启动。既然你已经写下了所有的步骤，那就回顾一下列表。请注意这些步骤中的每一步都与什么样的实际动作相关联。您可能需要向您的团队询问此事。复制是不是先把你的 as ssh 密钥放在某个地方？写下来。

是不是要先拷贝到盒子里或者其他安全的环境里？写下来。一旦它启动并运行了新代码，你怎么知道它是好的呢？你有没有检查过的图表？你只是刷新网页很多次吗？是否有您访问的健康终点？不管是什么，你猜对了，写下来。现在让我们从头开始看看这个列表。将会有多个步骤，其中可能会发生多个操作。一开始我们关注的是一切顺利，但是现在写下如果不顺利会发生什么。如果测试没有通过会怎么样？你开罚单了吗？你打开一个问题吗？当它进入生产阶段，看起来不太好，刷新不起作用，图形不满意或者运行状况不好时，该怎么办？你会复制二进制文件的旧副本吗？你会先重启吗？你看日志吗？不管是什么，把它写下来。

拿到名单了吗？好的很好。您现在已经为构建自动化和减少辛劳打下了坚实的基础，这样您的团队就可以专注于交付特性。

您创建的这个列表不仅仅是一个中间步骤，也不仅仅是一个一次性文档。现在，您有了一份新团队成员入职的基础文档。所以你有自动化。

你怎么想呢?留言评论！更多类似的请看这里:[https://thaiwood.io/DevTo](https://thaiwood.io/DevTo)