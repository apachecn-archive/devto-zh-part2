# 帮我避免让另外 2500 名网站访问者失望。

> 原文：<https://dev.to/_bigblind/help-me-prevent-disappointing-another-2500-website-visitors-58in>

大约一周前，我写了一篇关于[像布置房子一样布置你的代码库的文章，并发布到我的博客和 dev.to 上。](https://www.frederikcreemers.be/posts/code-layout)

当我在手机上查看帖子时，我注意到我的网站模板在窄屏幕上不能很好地处理宽代码片段。我开始着手解决问题，但当我看了看时钟，发现已经 0:30 了，我决定上床睡觉，第二天再完成。

我醒来时，收件箱里有一封电子邮件:

> 嘿，弗雷德里克，
> 
> 我在[https://www.frederikcreemers.be/posts/code-layout/](https://www.frederikcreemers.be/posts/code-layout/)因[https://news.ycombinator.com/item?id=17494218](https://news.ycombinator.com/item?id=17494218)偶然发现你的网站。一篇好文章。
> 
> 我只是想让你知道，因为你的 CSS，你的
> 站点有键盘可访问性问题:我不能用键盘滚动，直到我点击页面或按 Tab 键，因为可滚动区是#wrapper 而不是主体。
> 
> (我用的是 Windows 上的火狐。)
> 
> 我认为，这个问题可以通过删除下面的 CSS 规则来解决，不会有什么坏处:
> 
> [...]
> 
> 我建议比这更进一步，彻底废弃#wrapper，将其与 body 合并，并删除 html 和 body 上的几乎所有属性。
> 
> HN 论坛上的一些人也报告了该网站在 Android 和 iOS 上的问题，我猜这与此有关。
> 
> 除此之外，这是一个不错的、简单的、精益的网站。要是有更多像那样的就好了。👍
> 
> 此外，由于我没有仔细调查的原因，你的布局在 1001 像素和 1400 像素之间没有按照你的意图运行；这是使用 flexbox 进行布局的一个风险，尤其是使用 flex-wrap 时——如果您不仔细控制 flex-basis、flex-grow 和 flex-shrink 参数，当面对与您设计的内容不同时，它可能会做出令人惊讶的事情。您可能想研究一下 Grid，它现在得到了很好的支持；这样你可以对版面设计更有信心。

所以，我的文章被发布到了《黑客新闻》上，并且非常受欢迎(在我写这篇文章的时候得了 86 分)😲，但用户在一些设备上使用我的网站时遇到了问题😔，如果我在睡觉前完成了修复并将其推送到 live，其中一些是可以避免的。但我不会因为重视睡眠而责备自己，因为这是一个适用于所有设备的网站。

所以我查看了谷歌分析，发现我的网站已经有了 100 多名访客。总而言之，在 6 月 10 日星期二，我有 56000 名访客。其中大约 27000 人在 IOS 或 Android 上，这意味着他们可能在我的网站上有过糟糕的经历😰。

因此，我对我的网站的 CSS 做了一些更改，包括 Chris 建议的#wrapper 更改，并且我已经使用了 [CodePen 与 CrossBrowserTesting](https://blog.codepen.io/2014/12/15/test-pens-browser-live-free-crossbrowsertesting-com/) 的免费集成来尝试和改进我的网站在不同浏览器和设备上的可用性。

我在设备上看到很多我无法重现的问题，所以如果你能帮我解决，我会很高兴。

*   在任何设备上访问[https://www.frederikcreemers.be/posts/code-layout/](https://www.frederikcreemers.be/posts/code-layout/)，尤其是移动设备
*   截图和/或描述您可能遇到的任何问题
*   如果您有时间和兴趣，请帮助我了解为什么会出现这个问题。

感谢你愿意给予的任何帮助。