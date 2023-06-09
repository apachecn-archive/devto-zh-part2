# 值得注意的发展问题

> 原文：<https://dev.to/devteam/notable-devto-issues-up-for-grabs-4h64>

又见面了！👋我们又遇到了一些问题，需要寻求帮助。点击这里可以找到[的第一篇帖子。本周，我们将重点讨论这三个问题:](https://dev.to/devteam/notable-issues-of-the-week-up-for-grabs-19f7)

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 提高码头工人的速度 #923](https://github.com/thepracticaldev/dev.to/issues/923) 

[![maestromac avatar](img/285ef83c821e1c27f7d5dff77822d657.png)](https://github.com/maestromac) **[maestromac](https://github.com/maestromac)** posted on [<time datetime="2018-10-15T16:16:16Z">Oct 15, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/923)

**您的功能请求是否与某个问题相关？请描述一下。没有**

**描述您想要的解决方案**目前，从 macOS 上的 docker 实例加载一个主页大约需要 3 分钟，这是一段很长的时间<g-emoji class="g-emoji" alias="scream" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f631.png">😱</g-emoji>。 [Docker-sync](http://docker-sync.io) 可以成为解决这个问题的工具。

**描述你考虑过的替代方案**也许我们速度的罪魁祸首是我们糟糕的配置。

**附加上下文**不适用

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/923)

任何码头工人的帮助都将不胜感激。我们收到一些评论，说速度在 Linux 上似乎不是问题，所以它可能只是 macOS 的问题。也许是你遇到的问题？欢迎对这个问题发表评论。即使是关于我们的 Docker 设置如何在您的机器上运行的评论也会有很大帮助！

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 功能要求:夜间模式 #134](https://github.com/thepracticaldev/dev.to/issues/134) 

[![ghost avatar](img/29e48482b715470240aa6c57e15c1745.png)](https://github.com/ghost) **[ghost](https://github.com/ghost)** posted on [<time datetime="2018-01-10T11:09:43Z">Jan 10, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/134)

# 特征请求

如果有一个“夜间模式”，可以将网站的颜色切换到在某些情况下看起来更好的深色版本，那将是非常棒的。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/134)

夜间模式！！！万圣节就要到了，给 dev.to 一个黑暗主题会很棒。不管怎样，我知道我晚上的眼睛会喜欢它的。查看问题和评论以获得更多关于开始的信息。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 【更好的 UX】让屏幕阅读器用户在点击进入链接时更方便(a11y)  #969](https://github.com/thepracticaldev/dev.to/issues/969) 

[![meowwwls avatar](img/3c1546ef6591d117029e068530d21835.png)](https://github.com/meowwwls) **[meowwwls](https://github.com/meowwwls)** posted on [<time datetime="2018-10-20T04:15:23Z">Oct 20, 2018</time>](https://github.com/thepracticaldev/dev.to/issues/969)

**您的功能请求是否与某个问题相关？请描述一下。**在使用 Twitter 或 Github 登录时，screenreader 用户在浏览文档时只会听到“Twitter”或“Github”。这可能会令人困惑，认为该链接会将用户带到 Twitter 或 Github。

下面是一张 GIF 图，展示了当用户跳转到这些链接时，屏幕阅读器用户会听到什么:

[![A recording of using VoiceOver to tab to sign in links, where a user hears Twitter or Github with no context](img/a80484a05074f380346dc28d65ff23ee.png)T2】](https://camo.githubusercontent.com/e556e9382f8d1ddb011c7fb2f0923f4465d3e1ec/68747470733a2f2f692e696d6775722e636f6d2f4c7666754d53592e676966)

**描述你想要的解决方案**对屏幕阅读器用户来说，更好的体验是听到“使用 Twitter 登录”或“使用 GitHub 登录”。

**描述你考虑过的替代方案**一个简单的方法是使用一个特殊的`visually-hidden`类将它包装在一个`span`元素中，同时保持附加文本在视觉上隐藏:

```
<a href="/users/auth/twitter?callback_url=https://dev.to/users/auth/twitter/callback" class="cta cta-button" data-no-instant="">
  <span class="visually-hidden">Sign in using</span> TWITTER
</a>
```

Enter fullscreen mode Exit fullscreen mode

```
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  border: 0;
}
```

Enter fullscreen mode Exit fullscreen mode

通过以上操作，screenreader 用户的体验会更好、更准确:

[![A recording of using VoiceOver to tab to sign in links, where a user hears 'Sign in using Twitter' or 'Sign in using Github'](img/643518d1377dfd9a3c18a56c6fbbc6fc.png)T2】](https://camo.githubusercontent.com/2b450bab68833f2071b43af8d391ffb837ea7732/68747470733a2f2f692e696d6775722e636f6d2f497047784167582e676966)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/969)

这是本周出现的一个很棒的问题。这个问题写得很好，作者还提出了一个很好的解决方案。对于易接近性和 Hacktoberfest 来说，这是一个很好的低挂水果。

### 现在就这些；下周我们会有更多！