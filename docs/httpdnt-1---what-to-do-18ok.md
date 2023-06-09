# HTTP_DNT: '1' -怎么办？

> 原文：<https://dev.to/jacoby/httpdnt-1---what-to-do-18ok>

**T2`HTTP_DNT: "1"`**

**T2`navigator.doNotTrack: true`**

我总是说我做“网而不网”。我使用使用 88 和 443 端口的常见技术——HTML、CSS、Javascript、jQuery、Bootstrap、JSON、curl 等——但我的工作是创建接口，以便进行科学研究。我们不是广告驱动的，也不是普通访问，我们的用户可以为一笔交易花费数千美元。

因此，当我们有`HTTP_DNS: 1`时，我们也有`REMOTE_USER: "jacoby"`。所以，我说“这就是我；让我知道我做过和能做的一切”，但我也说“不要跟踪我”。

我可以说我只真正看过服务器日志文件两次。一次是因为…好吧，假设一个 URL `http://example.com/test/test.html`。您已经在`http://example.com/index.html`上，想要链接到 test.html。你可以去`test/test.html`或者`/test/test.html`，或者使用全票。或者，事实证明，你可以成为`http:///test/test.html`。我想。你为什么要做这种事？不知道。但我们的一些链接正是如此。而且，原来 Chrome 和 Firefox 确实(曾经？)的“正确的事情”，并认识到你指的是`http://example.com/test/test.html`，但(当时)Safari 没有。这看起来很简单，但是`$user`无法到达`$page`，所以用户是什么，浏览器是什么。

(我向 Safari 报告了一个“这真的很愚蠢”的错误，这个错误导致了票务追踪器崩溃，所以我也报告了这个错误。)

另一个……我不记得是否有与之相关的坏结果，我记得我发现一个佐治亚理工学院的研究生通过一个基于 IE 的中文浏览器进行连接(6？5?)发动机。

否则，我会看到错误日志，但我会看到时间、客户机 IP 地址(这大多无关紧要)，以及我的代码的一长串失败。嗯，大部分是。不管怎样，我都不理会他们。

这是我追踪的两个案例，一个以“这就是中国人的浏览方式”结尾，另一个是一个真正解决了的问题。

我知道除了`REMOTE_ADDR`、`REMOTE_USER`和`HTTP_USER_AGENT`之外，服务器端环境变量中还有很多可以使用的东西，如果我开始从 DOM 中提取一些东西，比如`window.innerHeight`、`window.innerWidth`和`navigator.platform`等。我大概可以区分两台来自防火墙后的机器，但是为什么呢？如果我试图从你的搜索中确定你是否是搜索`running bassett hounds`的人，这样我就可以在你的广告中放更多的狗图片，这是一回事，但我只关心一件事:你是向我们提交这份工作的人吗？

所以，我想我的问题是，无论是在我目前的职位上，还是在 2018 年成为一名网络开发人员的大背景下，我应该做些什么来尊重“请勿追踪”的标志？维护服务器级日志还是一件事吗？

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。