# 提高自动化测试的 10 种意想不到的方法

> 原文：<https://dev.to/endtest_io/10-unexpected-ways-to-improve-your-automated-tests-4i4a>

**应该用硒**还是别的？**我应该把所有需要自动化的东西列成一个清单吗？**我应该向**求助吗？**

听起来很熟悉？

我知道这种感觉。我也有同样的感觉。

无论你是初学者还是专家，这些要点都有可能帮助你(T4)将你的自动化测试技能提升到下一个水平。

这里有 10 种意想不到的方法来帮助你提高你的自动化测试。

### **1。拥抱不断变化的技术趋势**

编写你自己的自动化测试框架会让你的生活变成人间地狱。所以，你如何避免灭亡，接受创新？

[![bad code](img/e0a873549c728cf1929bdbdb5cfbb39c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OaxNDoMG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GC2F8qx.jpg)

最糟糕的是，你不会意识到这一点…至少等到为时已晚。

创建一个基本的测试套件是小菜一碟，但是在大发布之前，当你的 626 个测试中有 478 个由于不同的错误而失败时，你会喜欢维护整个代码库吗？

…

没错。

是的，在接下来的几天里，您的整个团队都将结束手工测试。很遗憾，是吧？

[![robot maintenance automated testing](img/3cf83e62268c27758a7077ff495feefc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--idG6aNjb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZzRBhoF.jpg)

如果你在想“那完全不是我”，问问自己是否有时间处理:
为你的测试创建一个稳定的跨浏览器云基础设施。
·实施图像比较算法进行目视检查。
为您的测试运行实现视频记录。
为您的测试实现一个每天运行的时间表。
将您的测试与您的 CI/CD 系统集成。

如果答案是**“是”**，你可以从这里开始不看了。

如果答案是**“不”**，下一个问题应该是**“好吧，还有什么选择？”**。
好消息是，公司已经开始迁移到提供测试基础设施的云平台(IaaS、PaaS 和 SaaS)。

[![software testing tools](img/fe6de9abc7e297480c9c80f389d9139d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MkWmGRCv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/C4tavcC.png)

在做了一些广泛的研究后，以下是我能找到的关于他们的信息:

[![software testing tools comparison](img/c5b850d11abe20abd68dc4f6b0b89a10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZbK0OS11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/W9bQPtk.png)

至于 Cucumber 或 Behat，它们只是图书馆，所以我在这里找不到它们的位置。

### **2。把稳定放在首位。**

如果你的测试通过了 99 次，失败了一次，底线是你的自动化测试是**不稳定的**，就像承认这一点一样令人不快。

[![developer life](img/7fda0df296971cbe1678512bb6001962.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2V5SgSId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/dEsdCE1.png)

拥有 2 个稳定的测试比 5 个不稳定的测试更好。那些不稳定的测试只会考验你的耐心，迫使你一遍又一遍地手动检查功能。

您已经知道，在您正在处理的测试用例完全稳定之前，继续进行下一个测试用例并不是明智之举。

萦绕在你脑海中的那种偷偷摸摸的**“反正我会回来修理它的”**不会发生，你最终只会做额外的工作来修理它。

[![big bug](img/949b7aa40063dc9874e4f553af3665a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FC8Qe5AH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/usmrd6F.jpg)

### **3。执迷不悟也没什么。**

不要忘记关注负面测试场景，那是 bug 爬行的地方，它们不喜欢干净的快乐路径。

在手动测试时，为你发现的每一个 bug 创建一个测试，这样你就知道它永远不会便宜你。

[![bugs after release](img/f76d11c021a4bba6d0d1117d8cddcc3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8pDJVihz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Hr9XiCV.jpg)

### **4。视觉效果很重要。**

即使您的测试通过 UI 与应用程序交互，并且您正在检查一些元素的存在，这也不意味着一切都是完美的。

[![visual testing](img/0a6d8be669165719314f8b359174e18d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RE92oMxa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Ldrta0j.jpg)

这就是为什么添加屏幕截图比较步骤非常重要，它可以根据现有的屏幕截图逐个像素地检查元素。

几个月前，我写了一篇简短的技术文章，你可能会觉得有用。

### **5。你。不能。自动化。一切。**

可悲的是，有些事情无法以稳定有效的方式自动化。我们越早接受这一点越好。以 **reCAPTCHA** 为例，您可能会发现自己需要为来自测试机器的请求禁用它。

[![captcha](img/74f66de6fc09828d23e902b06946fed7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qUEQg7LR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ihBnVUX.jpg)

纠结于自动化不可能只会浪费你的时间。幸运的是，有一些聪明的变通方法。你要做的就是找到他们。

### **6。跨浏览器:你确实需要。**

***“嗯，我们的网络应用在 Chrome 上运行得很好……
“反正大多数人都在用这个……”
“嗯，让测试在所有浏览器上运行需要一些时间……”
“一些用户报告了一些错误，可能他们用的是旧版本的 Chrome，或者……你知道，用户有时候就是很愚蠢。”***

好奇心驱使你四处打听人们用什么浏览器访问你的 web 应用程序的统计数据。

就是这样得到这个可爱的饼状图:
[![browser pie chart](img/b789a1d32161bd7ca9a60cc058ad7116.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DnG_BPPu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Z2zoGYK.png)

你好吗？

这是当你知道你需要跨浏览器，真的没有其他路可走。

[![browser wars](img/4cde54d5ae3d539fb05589d7d6332571.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KPigkZ7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/GXRRNXS.jpg)

这可能很复杂，有时还很昂贵，但总是值得的。

如果你要去，那就全力以赴:
不要依赖无头浏览器。
在 Chrome、Firefox 和 IE 中使用 Windows 机器进行测试。
在 Edge 中使用 Windows 10 机器进行测试。
使用 Mac OS 机器在 Safari、Chrome 和 Firefox 中进行测试。

如果你要去无代码化，目前唯一能提供上述功能的平台是 [Endtest](https://endtest.io) 。

### **7。记住那些小事。**

有时候，我会忘记那些重要的小事。当你认为自己已经完成时，可以问自己以下几个问题:

你最后一次从页面源测试 META 标签是什么时候？你最后一次测试饼干是什么时候？

这些东西对于排名、营销和跟踪的目的来说出奇的重要。

[![web visitor tracking](img/8460e34f867226654c7de88ccd9febd2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o1UmTP44--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8gqn9fj.jpg)

### **8。有时候，移动是第一位的。**

那些从事 B2C 产品的人已经知道，大多数用户通过他们的移动设备访问他们的产品。那些从事 B2B 产品的人需要开始考虑同样的事情。

[![mobile testing](img/1d47fb83480ab592d13b7aa0a794dfd1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--whHVHx1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/42klXPw.jpg)

### **9。不仅仅是测试人员，每个人都应该编写测试。**

因为产品所有者和业务分析师是最了解软件和流程应该如何工作的人，所以他们是参与测试编写的最佳人选。

当编写幸福之路的测试时，他们的观点总是有用的。

[![product owner](img/9dc6b63386cd664d022481fb18ce30b0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S7YGwD_L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/WWwmAAW.jpg)

这种体验是双赢的，因为它也帮助他们获得更多关于他们的用户故事是如何实现的洞察力。

但是他们可能不知道如何写代码…T3】

对于这种情况，如果你不愿意花几个月的时间教他们如何做，那么去[无代码](https://endtest.io)是唯一的办法。如果是的话，我不得不说我钦佩你的耐心。

### **10。不要害怕寻求一点点帮助。**

由于整个公司都将从这些自动化测试中受益，如果你需要一点帮助，每个人都会很高兴地加入进来(即使你更了解**！).**

 **[![peer programming](img/946514b930c4ac3302a7ddf1c9f0b3f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xv4sA-uP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/4GUSKQR.jpg)**