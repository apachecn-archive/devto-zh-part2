# 跟上浏览器更新

> 原文：<https://dev.to/raymondcamden/keeping-up-with-browser-updates-3jo7>

这是我已经在脑子里思考了将近两年的事情。我向一些出版物和会议推销过它，但从来没有成功过，所以我想在这里简单地写下我的想法会很好。作为一个几乎从第一天起就进入这个行业的 web 开发人员，我对 web 已经取得的进步和我们现在的平台有多棒感到非常高兴。这当然不是说一切都很理想，但我们已经走了很长一段路，我认为这值得庆祝。

我们平台发展的核心方式之一是我们的浏览器。这可能是一个显而易见的观点，但具体来说，更新的速度对消费者和开发者来说都是一个巨大的好处。我仍然记得每年发布新的浏览器版本是一件大事。现在我们的浏览器(大部分)是自动更新的，而且几乎是持续更新的。在一年多的时间里，我无法诚实地告诉你我使用的是哪个版本的 Chrome。那就是我试图集中注意力。现在，当我想到浏览器时，我倾向于更多地考虑它们的一般支持级别和功能，而不是“X.0 版本”，而不是一个特定的数字。

当然——这带来了一点问题。假设我们的浏览器可能在一个月内更新多次，而不是一年，我们如何才能看到发生了什么变化？

需要澄清的是，我不只是在谈论新奇的 JavaScript APIs。面向消费者的变化也非常重要。例如，Chrome 一直在以相当显著的方式改变其识别安全和非安全网站的方式。再比如，Firefox 最近在他们的浏览器中加入了截图工具。这两件事都与代码无关，但肯定很重要。

问题是，这些浏览器能很好地呈现这些信息吗？在我看来，浏览器厂商应该做几件基本的事情。正如我所说的，这是我的观点，但你在我的博客上，所以你应该期待它！

*   浏览器本身应该会引导您找到这些信息。我的意思是——我不应该用谷歌来找它。这是我的第一条规则。如果你“希望”你在 Twitter 上的关注者看到一些你没有看到的东西。句号。
*   这些信息应该包括面向消费者的变化和面向开发者的信息。
*   高层次的覆盖是绝对好的——记住这些是发行说明。链接到有更深入的例子和文档的博客帖子也是完全可以的。

所以基本上-不要让我打猎，包括开发/非开发，给我一个高层次的总结，我可以选择我关心的主题。

抛开这些不谈，让我们来看看主流浏览器是如何处理的。

### 火狐

我从 Firefox 开始有两个原因。首先，几个月来它一直是我的主要浏览器，其次，它是我做好工作的“黄金标准”。

Firefox 在其“关于 Firefox”窗口中提供了更新信息的链接:

[![Firefox About text](img/4a4327e45e5ab9e690aab545d40e937f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t1Qnjrw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b1a.jpg)

带你来到这里:[https://www.mozilla.org/en-US/firefox/60.0.2/releasenotes/](https://www.mozilla.org/en-US/firefox/60.0.2/releasenotes/)

[![Firefox Release Notes](img/e4601520a1adf5d0906c7d14fed87b97.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Et7XB1y7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b2.jpg)

首先，从设计的角度来看，这是一个非常干净，而不是压倒性的页面。默认情况下，它关注消费者的变化，这是有道理的。另请注意顶部的标签栏，它也可以让您看到移动版本的更改。

有点难看，我觉得应该更显眼，但是在“其他资源”下的右下角是[【开发者信息】](https://developer.mozilla.org/en-US/Firefox/Releases/60)的链接。在这里您可以找到您需要的另一套文档。请注意，他们在将变更分成不同类别方面做得非常好。这可以让你跳过你可能不关心的东西:

[![Firefox Developer Changes](img/ca5a38ff833a5c3b63771bb9133dfc91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6PiMdGGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b3.jpg)

在面向消费者和开发者的页面上，它们也很好地链接到早期版本，以防你需要更深入地挖掘过去。我知道我之前说过，但是，我喜欢这些页面的设计和组织。我要做的唯一可能的改变是在初始页面上突出显示开发者信息链接。

### 铬

啊，铬合金。在过去一年左右的时间里，我不断地让他们升级游戏，给他们的开发者支持者/工程师带来了一些麻烦。我正试图停止，因为它越来越令人讨厌(我敢肯定)。Chrome 是排名第一的浏览器，拥有令人难以置信的市场份额，但我认为他们需要在这方面做得更好。

首先，“关于 Chrome”页面很少，在我看来，这浪费了很多机会。

[![Chrome's About Page](img/6ec8d7c50c4c12a1a98be352885e32f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b7IdrSIO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b4.jpg)

其中一个与帮助相关的链接指向消费者信息，但不是任何发行说明。好吧，那我们还能尝试什么？如果你打开 devtools 并且最近安装了一个新版本，你实际上会得到一个自动的附加面板，上面有发行说明。我没有这个的截图，但是做的还算不错。在 devtools 中，您也可以单击三点菜单，转到帮助，然后获得发行说明的链接:

[![Chrome's Devtools Menu](img/b14b3ec19371a14feffcbcffcad8aa80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mdtafvYT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b5.jpg)

这当前链接到特定于当前版本的[https://developers.google.com/web/updates/2018/04/devtools](https://developers.google.com/web/updates/2018/04/devtools)。如果你点击页面顶部的“更新”，你会看到这里:【https://developers.google.com/web/updates/】T2。

[![Chrome's Update page](img/487cb19968f83374d74800c4a87c9df8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PbmK2AAf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b6.jpg)

好吧，所以从技术上来说，这不是一个“版本更新”类型的页面，更一般，但是，我真的很喜欢它。你有一篇专门关于 Chrome 67 变化的文章，但也有关于 PWA 变化的标注。很好的搭配。您还可以在“按年份”选项卡上进行过滤，以进行更多的历史浏览。

从技术上来说，这也是部分面向消费者的东西，但它不一定感觉像是以这样一种方式组织的，以便更容易区分。也可能只是我看到的文章的当前组合。

最后…有一个跟踪变化的好工具: [Chrome 平台状态](https://www.chromestatus.com/features)。

[![Chrome Platforum Status](img/73156c8405b8733e5b219f3670a8f887.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XSy8tcgz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b7.jpg)

这提供了一种快速查找特定功能并过滤到特定版本的方法。我真的希望这个链接来自那个巨大的，空的，关于页面，只是为了帮助发现。如果你知道这个页面，我很乐意在下面的评论中听到我的读者的意见。

哦，还有 Chrome…解决这个问题:

[![Chrome Platforum Status](img/bd7301a2fd2808ca8eb592e352910a1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XqxzXEXI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b8.jpg)

### 微软 Edge

在我开始之前，如果我看到一个“Internet Explorer 6 做了一些无聊的评论”，我会去呕吐。是时候放下 IE 了。Edge 是……一款相当不错的浏览器。如果 Firefox 最近没有取得很大的进步，我可能会用它取代 Chrome。我确实想花更多的时间在 Edge 上，最近我已经将一些网站“专用”于该浏览器，作为强迫自己使用它的一种方式。

如果你点击 Edge 的“三点菜单”(顺便说一下，我们叫它什么，不是汉堡菜单，而是…？)您将看到一个“最新动态和提示”链接。带你到这里:[https://microsoftedgetips.microsoft.com/en-us/?source=menu](https://microsoftedgetips.microsoft.com/en-us/?source=menu)

[![Edge Devtools What's New Page](img/e2599d4580125d3fb2e8315230423273.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bx4gceoT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b9.jpg)

它被顶上的英雄横幅压得很低，但你可以看到重点是消费者面临的变化。

如果你打开 devtools 并点击那里的帮助图标，你会到达这里:[https://docs . Microsoft . com/en-us/Microsoft-edge/dev tools-guide？source=f12help](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide?source=f12help) 。这是他们 devtools 的核心文档，但第一个子菜单项是一个“新功能”，带你到这里:[https://docs . Microsoft . com/en-us/Microsoft-edge/dev tools-guide/whats-new](https://docs.microsoft.com/en-us/microsoft-edge/devtools-guide/whats-new)。你可以把它加入书签，作为一种快速查看新内容的方式。在这个页面上还有一个“开发指南”，如果你打开它，还有一个新内容:[https://docs . Microsoft . com/en-us/Microsoft-edge/Dev-Guide/whats-new](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide/whats-new)。

[![Edge What's New Page](img/96b60e6e9cc0cd49297b1ae7cfcd9be0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9LqiTai_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b10.jpg)

如果你喜欢“EdgeHTML”并且对它有点困惑，这只是 Edge(和其他组件)的渲染引擎的名字。你可以把它想象成 Chrome 的 Blink 或者 Safari 的 Webkit。

微软因在演示中使用 CodePen 而获得加分。我刚注意到这一点，真酷。

### 游猎

所以，嗯…你看…我是一台 Windows 电脑，虽然我有一台 Mac，但我已经一个多月没开机了。因此，我不能谈论“开箱即用”类型的体验在可发现性方面的问题，我可以尝试分享我通过搜索找到的东西。当我搜索“safari 浏览器新功能”时，第一个链接看起来很完美:

[![Safari](img/bdb4b9f9a04d651213f3bf74fef0ac17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fuy5pLwO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b11.jpg)

带你到这里:[https://developer . apple . com/library/archive/release notes/General/WhatsNewInSafari/Introduction/Introduction . html](https://developer.apple.com/library/archive/releasenotes/General/WhatsNewInSafari/Introduction/Introduction.html)

你首先看到的是:“重要的是:这份文件不再被更新。有关 Apple SDKs 的最新信息，请访问文档网站。”叹息但是他们确实链接到了正确的地方:【https://developer.apple.com/safari/】T2

[![Safari Help](img/0e432d7d8567989276807129332e75a8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--11fsmQSP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/b12.jpg)

这里有一个很好的[“新功能”](https://developer.apple.com/safari/whats-new/)链接，似乎涵盖了消费者和开发人员的信息。

总的来说，这看起来不错——但是我不确定在哪里可以找到更老的信息，比如 Safari 11。

### 总结起来

我希望这篇小文章能有所帮助。我没有包括其他浏览器，如 [Brave](https://brave.com/) (这很好，这是我在 iOS 上的默认设置)，但我很高兴看到人们在下面的评论中分享信息。