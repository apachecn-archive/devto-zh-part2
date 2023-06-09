# 测试:ElementalSelenium 的 5 天挫折和探索

> 原文：<https://dev.to/arleneandrews/testing-elementalseleniums-5-days-of-frustrations-and-explorations-4h35>

[![](img/386318f46465e1be962a699641d291e2.png)](https://www.velior.ru/wp-content/uploads/2009/05/Test-Computer-Key-by-Stuart-Miles.jpg)
[https://www . velior . ru/WP-content/uploads/2009/05/Test-Computer-Key-by-Stuart-miles . jpg](https://www.velior.ru/wp-content/uploads/2009/05/Test-Computer-Key-by-Stuart-Miles.jpg)

经过几个月的尝试，发现自己擅长什么和不擅长什么后，我找到了自己想追随的兴趣:测试。有如此多的新事物出现，随着我们开始看到的联网设备，加上来自我们认为值得信赖的来源的问题的可能性——这听起来像是一个我可以享受、擅长和成长的职业。

对于那些读过我早期作品的人来说，这听起来可能有些奇怪——几乎从这个博客开始，我就一直在抱怨公共课程中缺乏测试教育。但是，正如一些人指出的那样(偶尔直言不讳)，如果有需求没有被满足，是什么阻止了我去满足它呢？

所以现在是向前迈进的时候了，并贡献出我认为有用的信息。

有一次我提到我想以此为职业进行探索，有人慷慨地给我介绍了元素硒。该站点不仅提供了测试技巧(如果您在 Selenium 中工作，您可能会喜欢这些技巧，并且文档是开放的)，而且还提供了一个免费的课程，让您了解它到底做了什么，以及如何做。

是啊！这个特殊的群体知道我喜欢较短的课程，也知道学习资源的预算是多少(即没有)，所以我当然立即报名参加了。我一直在做一个学习项目，不确定如何测试其中的一些页面——这似乎是一个完美、简单的答案。他们提供了多种语言供选择，这让它变得更好:我的应用程序是 Python，所以我选择它。已经建立了一个虚拟的环境——不妨养成好习惯。

只需要安装两件东西？这就更好了——我可以做好准备，随时出发。

经过一点介绍，以及寻找测试元素的技巧(并做笔记回去把这些添加到我的应用程序:脸红: )第一个测试已经写好了，准备好了。我当然要运行这个！一个错误出现了——没什么不寻常的:这一天充满了错别字，甚至是我自己的名字，或者单词<u>中的</u>也是如此。因为，为了开发肌肉记忆来获取这些，我手动输入了这个测试。我改正了明显的错误，继续上课。

这看起来很合适，也很适合测试我的应用程序——我期待着课程结束，并着手编写自己的测试。

又一次失败了。

而这个我不知道该怎么办——浏览器显示出来了，却说找不到配置文件？

好吧，这是学习时间，肯定的。是时候找出问题的原因了。我猜在测试平台上测试找出错误是学习的一部分。我仍然很高兴，即使技术上是时候停下来了，我还是会继续。

这似乎很常见——我感觉没那么糟糕:可能有几种解决方案，所以我可以缩小我的错误在哪里，并且知道<u>为什么</u>它会发生。事情变化得如此之快，我的第一个猜测是它是系统中某个东西的更新，或者可能是一个小的版本修正移动了一个引用。

在寻找了一段时间后，我发现，也许答案是我需要一个框架——这里有一些选择。开始下载/安装，然后就到此为止——这应该可以让它在早上工作！

我还找到了 Gekodriver(它需要放在虚拟信封的 bin 文件夹中，以及 Windows 中的主 Python 文件夹中——这对于一些惊慌失措的人来说是个问题——这至少给了我一个不同的错误！以及我安装 tar 文件已经有多长时间了

更持久的测试？是的**请**！哦，我真希望这能成功。一杯新鲜的茶，然后回到研究中。即使我今天的打字还算合格，我也希望我的测试能够正常工作，该死的！

好吧，这个看起来很简单:堆栈溢出问题的答案。是的，这看起来像是我的问题——阅读答案，并通过[达摩克里希](https://stackoverflow.com/users/5077406/dharma-krish)找到一个:指出一些显而易见的事情。如果你有最新的 Firefox，使用最新的 Selenium 驱动程序。好吧，那会有帮助的-而且很明显。上面是设置 Firefox 配置文件的方法(5 行而不是 1 行——我接受！)

> fp = webdriver。FirefoxProfile()
> 
> (这里“2”代表“自动代理配置”)
> 
> FP . set _ preference(" network . proxy . type "，2)
> 
> FP . set _ preference(" network . proxy . auto config _ URL "，"[http://proxy-address-here:8080/"](http://proxy-address-here:8080/%22))
> 
> driver_ = webdriver。Firefox(firefox_profile=fp)

深呼吸，尝试第二天的代码...它会加载并找到一个配置文件(！)，并且测试通过！

因为我已经准备好了第二天的代码，一点复制和粘贴就完成了第四天代码的新变化。在确保文件到达正确位置的一些糟糕的开始之后，我现在有了可以在 Ubuntu 和 Windows 中工作的代码。

如果你有这个问题，至少在下一次更新之前，这可能对你有帮助。

*   确保您安装了测试框架
*   使用所有程序的最新版本
*   现在，确保 Gekodriver 在路径中(虚拟信封的 bin 文件夹，以及 Windows 中的主 Python 文件夹)
*   如上设置你的火狐档案——如果需要，你可以进一步修改它
*   玩得开心点。

第五天的课程似乎是为多种浏览器设置的——我现在要庆祝它能在一种浏览器中工作。