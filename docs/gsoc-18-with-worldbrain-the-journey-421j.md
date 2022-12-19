# GSoC '18 与世界大脑:旅程

> 原文：<https://dev.to/digi0ps/gsoc-18-with-worldbrain-the-journey-421j>

你好世界。这是 Sriram 从一个真正令人兴奋和富有成效的夏天中走出来。今年夏天，我通过 WorldBrain 被 GSoC 选中，去开发他们的浏览器扩展 Memex。事实证明在那里工作很棒。下面是我整个 GSoC 旅程的博文

### 前 GSoC

去年，作为一名大学新生，我非常渴望进入 GSOC。为了向前迈一步，我给了祖利普一枪。虽然结果不太好，但它给了我一次奇妙的学习经历。你可以在我不久前写的这篇文章中读到更多。

随着进入 GSoC 的希望消失，我不想浪费整个夏天。我决定学习一些新的东西，并选择了当时最流行的框架，[反应](https://react.com)！整个夏天，我对它有了相当的了解，甚至在 React 中完全重构了我的旧网站。很快，我开始在 React 中从事更多的项目，增强了我在这个领域的信心。

时光飞逝，很快就到了 18 年 2 月。GSoC 组织名单出来了，我仍然不知道该选择哪一个。

### 世界大脑

我在 GSoC 的 18 个组织列表中滚动，专门寻找涉及 React 的项目。我发现 WorldBrain 和他们对网络的[愿景](https://medium.com/@WorldBrain/where-we-are-heading-with-worldbrain-65f244f540b8)吸引了我的目光！

**WorldBrain** 是一个开源软件集合，其使命是使人们组织、恢复、分享和发现网络上最有用和知识扩展内容的速度提高 10 倍以上。 **Memex** ，这是他们的第一个产品，是一个免费的浏览器扩展，它解决了在线研究中最令人沮丧的体验之一:再次组织和查找网站。

我非常兴奋地尝试他们的扩展。我做了，哦，天哪，我喜欢它，作为一名学生，它被证明对找到我以前已经访问过的网站很有帮助。很快，我把自己介绍给了我的同龄人，他们碰巧非常友好。首先，我必须从他们的想法列表中选择我想做的项目。尽管我面前有许多有趣的想法，但我还是选择了那个看起来相对容易实现的想法。:D

这个项目的想法是将 Memex 的搜索结果和 Google 的搜索结果放在一起。基本上，我必须让内容脚本在所有页面中运行；如果页面的 url 与 Google 的搜索 URL 匹配，则获取 Memex 结果并将其呈现在页面上。所有的讨论，实施。而这个特性的代码可以在[这个 PR](https://github.com/WorldBrain/Memex/pull/320) 中找到。

<figure>[![](../Images/c731b4140ed0db5ce00a3270c2bc7875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q-Zacn2H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3N2StiScvmk7FGAnM8XL0Q.png) 

<figcaption>Memex 结果与谷歌搜索结果一起注入</figcaption>

</figure>

### 关于项目

尽管上面提到的是一个 GSoC 项目，但我在 GSoC 之前就完成了它，这导致我把我的项目改成了现在的样子- [注释和注解](https://github.com/WorldBrain/Memex/issues/301)；在[奥利弗](https://github.com/oliversauter)的帮助下。

世界大脑的愿景是*拥有一个 p2p 社交网络，用于协作式网络研究*。其中一个重要的步骤是，人们需要与精彩内容、评论或评级等内容进行互动。这些互动将帮助用户更多地了解他们访问的每个网站的真实性、质量和有用信息。

我的项目通过为此添加基础工作来帮助实现这一愿景。它增加了高亮显示互联网上任何一段文本并将其与评论一起存储的功能。用户将能够在他访问的每个页面中看到他所做的评论/注释。随着这一点的确立；诸如在用户网络内共享注释、给出质量评级等特征。将来可以很容易地添加。

### 工作💻

快进到编码阶段: [Vincent](https://github.com/ShishKabab) ，WorldBrain 的一位出色的开发人员，已经为一个名为 Memex Link(以前称为 DirectLink)的新 Memex 功能建立了后端，该功能允许用户高亮显示一段文本，并获得一个分享它的 URL。这真是太酷了。所以我今年夏天的第一个任务是编写代码，在每个页面上显示 Memex 链接工具提示( [PR #398](https://github.com/WorldBrain/Memex/pull/398) )，并使演示 UI 更漂亮([链接到我所有与此任务相关的 PR](https://github.com/WorldBrain/direct-linking-backend/pulls?utf8=%E2%9C%93&q=is%3Apr+author%3Adigi0ps))。很快 [Memex。Link](http://memex.link) 投入生产，结果证明这是一个非常酷的功能。

<figure>[![](../Images/6e2e7cfb362daa2cbd00dd09ec3f3fd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vRNNqgY1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aeqjg3orqa_QPxCcJaZCDiA.png) 

<figcaption>带有注释和分享按钮的 Memex 工具提示。</figcaption>

</figure>

到这个时候，我已经开始添加一个侧边栏来显示每个网站上所有用户的注释。这个侧边栏有两个版本:一个在 Memex 的概览页面上，另一个作为每个网站的覆盖图。与后者相比，前者的启动和运行相对容易。在简短的讨论之后，我决定采用 iFrame 的方法。通过 content_script，我将注入一个按钮(称为 Ribbon)和一个 iframe(加载作为单独 html 存储的侧边栏),当用户单击 Ribbon 时，它会显示侧边栏。经过一番努力，我完成了最初的设计，并在 overview 和 iFrame 上运行。

[![](../Images/4c0c5cc9a1af7f6676134cbe6c1f2d52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8d6EgzAd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AaUp15LrE3uNIX74L.png)

我计划下一步处理注释的存储，但是最初的设计并不令人满意，我被给予了一个 UI 大修来工作。接下来的几个星期，我致力于 UI 和所有出现的与样式相关的错误。奥利弗希望它像素完美。尽管完成所有的细节是一场斗争，但它产生了一个更加直观和美观的用户界面。

<figure>[![](../Images/bbedbbf4c9bc9be3966796565ade49b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DMWfX0Tj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APcKoT8JdN6FNZ-I-ZlUeRQ.png) 

<figcaption>Memex 侧边栏，新用户界面位于包含少量注释的网页顶部</figcaption>

</figure>

接下来是存储带标签的注释，这与前面的部分相比进行得很顺利。存储部分完成后，MVP 就快完成了。最后，我发现最令人疲惫的部分是框架和它的父页面之间的交互(例如:突出显示/滚动 iFrame 中的注释容器)。

尽管已经存在在扩展脚本之间通信的方法，但是还没有任何方法支持网站和 iframe 中的扩展页面之间的通信。所以我写了自己的[类](https://github.com/WorldBrain/Memex/blob/develop/src/sidebar-overlay/messaging.ts)，作为窗口的包装器。postMessage 允许在两端执行远程函数。

### 生产🎉

在大量漂亮的功能添加、反响和错误之后，我们终于把评论和注释放到了作品中。🎉它已经上线，目前有 8000 多名 Memex 用户在使用。下载他们的 Chrome/Firefox/Vivaldi 扩展来看看吧。看到我的作品被其他人用于生产，这是一种非常有意义的感觉！❤

最后一周致力于清理、重构和编写测试。在我完成这篇文章的时候，我正在用 Jest+puppet er 添加 UI 测试。

### 演示

[https://www.youtube.com/embed/7aZZmw7warI](https://www.youtube.com/embed/7aZZmw7warI)

[https://www.youtube.com/embed/2VhlZhqJWKI](https://www.youtube.com/embed/2VhlZhqJWKI)

### 重要环节

[注释/标注拉取请求](https://github.com/WorldBrain/Memex/pull/411)

【GSoC 期间的所有拉取请求

[GSoC 提案](https://storage.googleapis.com/summerofcode-prod.appspot.com/gsoc/core_project/doc/5927534777597952_1522166272_Comments_and_Annotation_Proposal_by_Sriram.pdf?Expires=1534268084&GoogleAccessId=summerofcode-prod%40appspot.gserviceaccount.com&Signature=NQziGNSdZN5dT77oHg5v54kT6%2B7zlK6ZW%2FgF4IwUO2lxh22lWjwvmvTYYEwGcXXMXUFMjjgCorgHMec4zKWoCZEF7Qd8WI5U298MYuxq3YJCAgCFz67ypQv%2BeYUTLS94Vx6AKcDhzbZ2ndn1fs8Cs3k9gRvHxh10Vdm9CJ9Be%2BAIH0hnVOqqI6vwTl0cyf5oBJuaGHVUVdqLAYpGRhC3tnkjkHCejbV13RPT%2FShS%2FsrFj97kA2mJYcGkEZNzQnrgzTDzUEiVwiuq5Wa9yj5pqqpPvw14PVqVxBtkJFdgZZzK8Bb7IyoxSFhTrzqfWFCUmAEea9r3kalf0DcKrH5RxQ%3D%3D)

[我的 Github](https://github.com/digi0ps)

### 总结一下

通过 GSoC 的课程，我学到了很多东西。

*   在专业环境中工作让我接触到了很多新的礼仪，比如 git flow、单口相声等等。
*   意识到干净的代码和有组织的文件结构的需要。当我在处理大型项目目录时，这种重要性是显而易见的。
*   由于一些愚蠢的打字错误或疏忽，出现了许多错误。所以我意识到在推广之前进行彻底的测试是非常重要的。

### 结论

与世界大脑的每个人一起工作是一次难忘的经历。我犯了很多错误，有时我会懈怠或者不能完成所有的任务，但是感谢每个人，特别是奥利弗、文森特和乔恩，他们总是在那里帮助我，指导我！

~ Sriram