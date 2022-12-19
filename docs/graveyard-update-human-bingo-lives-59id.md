# 墓地更新:人类宾果生活！

> 原文：<https://dev.to/isaacdlyman/graveyard-update-human-bingo-lives-59id>

首先我写了这个:

## 文章不再可用

然后发生了这样的事:

> ![masukomi profile image](img/7a6c934cb0dc900a5c88de9e1c0c7a00.png)..发现后把它们都划掉。
> 
> 某网站生成卡片:
> [isaaclyman.com/human-bingo/](https://t.co/Nc7QZ3aKNn)by[@ isaacandsuch](https://twitter.com/isaacandsuch)2018 年 8 月 08 日 20 点 51 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1027296301690630144)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1027296301690630144)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1027296301690630144)

[![djquecke profile image](img/e7b9f2941dcb8f127875029f635f0c24.png) ](/djquecke) [ DJ Quecke ](/djquecke) • [<time datetime="2018-08-11T03:50:25Z" class="date-short-year"> Aug 11 '18 </time>](https://dev.to/djquecke/comment/4jde) 

请完成人类宾果 app)

所以我决定看看我能以多快的速度将 Human Bingo，一个以前基于 jQuery 的 web 应用程序，变成一个原生的 Android 应用程序(以前没有什么经验)。

事实证明，答案是“大约三天”这包括学习 Kotlin 所花的时间，因为我不喜欢 Java。

以下是最终结果:[Google Play 上的人类宾果游戏](https://play.google.com/store/apps/details?id=com.isaaclyman.humanbingo)

这是 GitHub 库(该应用是开源的):[GitHub 上的人类-宾果-安卓](https://github.com/isaaclyman/human-bingo-android)

这是一个非常简单的应用程序，无需登录，没有在线功能，没有高清图形，没有设备上的数据存储，也不需要任何权限。我确信一个更有经验的开发人员可以在一天内完成它。但是我自己花时间做了一些很酷的事情。

*   Android Studio，用于制作 Android 应用程序的 IDE，真的很棒(而且比四年前，我上次尝试制作 Android 应用程序时好了这么多)。
*   当你离开网络时，支持传统浏览器(比如 IE8)的问题并没有消失。为了获得大量受众，Android 应用必须针对许多不同版本的操作系统。幸运的是，开发生态系统内置了许多向后兼容性。
*   Kotlin 是一种有趣而优雅的语言，拥有非常有用和全面的标准库。
*   大多数关于 Android 应用的教程和问答都是为 Java 编写的，所以有时你必须自己找出等价的 Kotlin。但是如果你复制并粘贴 Java 代码到 Android studio，它会为你提供转换文件。
*   创建和发布一个 Android 应用程序就像创建和发布一个网站一样简单/困难。

如果你喜欢观察人，那就拿起这个应用试试吧，或者你也可以去 GitHub 做点贡献。

谢谢大家的鼓励。