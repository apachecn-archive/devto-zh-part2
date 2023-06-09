# 32 行代码

> 原文：<https://dev.to/jvarness/32-lines-of-code-4bgc>

在 Cerner，我们工程师非常努力地提供创新的医疗保健解决方案。然而，我们也玩得很辛苦！

> ![unknown tweet media content](img/48fb22a77cfe054618da62fdb01967a5.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1055901332345929735/pu/pl/8IuwYofQW_lSC5SX.m3u8?tag=5" type="application/x-mpegURL"></video>![Cerner Engineering profile image](img/fb3155197bc96ba8f6b933464a0203e5.png)cerner engineering@ cerner eng![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我们喜欢这种可视化效果 [@che55er](https://twitter.com/che55er) 将整个 2^5 编程大赛中的 [@github](https://twitter.com/github) 活动以及被更改的语言类型(通过文件扩展名)

在 Cerner 的内部黑客马拉松列表中，有一个名为 2^5.的编程竞赛这个比赛的规则相当简单:在 32 天内，你可以每天提交一份 32 行或更少代码的作品。

> ![unknown tweet media content](img/b9631070b9cfee407f95ff3ae168073f.png)![Cerner Engineering profile image](img/fb3155197bc96ba8f6b933464a0203e5.png)cerner engineering@ cerner eng![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)今天，我们庆祝了以工程生产力为主题的 2^5 编程竞赛的结束，在这场竞赛中，我们的工程师们分享了在过去 32 天中通过减少 32 行或更少的代码来提高生产力的方法。今年我们收到了 59 种不同语言的投稿！2018 年 10 月 25 日 20 点 43 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1055560488594087940)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1055560488594087940)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1055560488594087940)

这是我第二年参加比赛，我不得不说...超级好玩。以下是我在今年的比赛中学到的一些东西:

# 32 行可以强大

您是否曾经部署过一个不超过 32 行的企业应用程序，而您从来不需要维护它？我也没有。

然而，令人难以置信的是，你可以用 32 行代码制作简单的应用程序。以我使用 Vue 的[提交为例。Todo App 是一款非常经典的应用，可以用来替代 web 开发。](https://github.com/jvarness/cerner-2-to-the-5th/tree/master/2018/JS/vue-todo)

这个小应用程序只是使用 [Vue](https://vuejs.org/) 和 [bootstrap](https://getbootstrap.com/) 来创建一个小应用程序，以一种看起来有些视觉吸引力的方式列出所有你必须做的事情。它会同步到云端吗？没有。我能给每个项目添加一个按钮让你删除它吗？否(提交的内容应该超过 32 行)。这是学习两种非常流行的 web 技术的好起点吗？确实是的。

即使你的 32 行没有真正做任何“酷”或“值得注意”的事情，它仍然可以作为扩展你的知识的一个尝试。这就是我提交的许多 2^5 作品的目的:学习我以前不知道的东西。

# 网络现在拥有了你最喜欢的语言

不相信我？看看我提交的材料吧:

## [釜底抽薪](#kotlin)

如果你编写了任何 Kotlin 代码或者阅读了文档，你就会知道 Kotlin 可以被编译成 JavaScript 来允许你编写前端 web 应用程序代码，并且它也正在成为(如果不是已经)编写原生 Android 应用程序的事实上的标准。

但是您知道您也可以使用 Kotlin 来创建 web 服务吗？直到我发现了用于制作 web 服务的 Kotlin web 框架 [ktor](https://ktor.io/) ，我才明白。

我能够使用 Kotlin 编写小代码片段来编写 [web 服务](https://github.com/jvarness/cerner-2-to-the-5th/tree/master/2018/Kotlin/hello-ktor)以及 [web 套接字](https://github.com/jvarness/cerner-2-to-the-5th/tree/master/2018/Kotlin/ktor-sockets)。

对于那些可能使用 Kotlin 编写应用程序的人来说，这真的很棒，因为这意味着您的整个技术堆栈都可以用 Kotlin 从前到后编写！

## 雨燕

我不会写关于科特林的文章，如果我不给予苹果粉丝一点点关注的话！

如果你已经在 [Swift](https://developer.apple.com/swift/) 中编写了代码，你可能知道你可以使用 Swift 和苹果的原生库编写 iOS 和 macOS 应用。但是，您是否也知道您也可以使用 Swift 创建 web 服务？

我在 Swift 提交的一个作品使用了一个名为 [Vapor](https://vapor.codes/) 的库来编写一个计算第 n 个 fibonnaci 数的小型微服务。

苹果工程师现在也可以用 Swift 编写他们的整个技术堆栈了！所有这些创新对开发人员来说都是好消息，因为他们不必为了完成他们需要做的工作而投资多种语言。

## 其他

当然，对开发者来说，Kotlin 和 Swift 并不是唯一预示好消息的语言。我没有时间用所有这些语言提交材料，但是。NET Core 是另一个很好的选择，还有 [Dart](https://www.dartlang.org/dart-vm) 供你们这些 [Flutter](https://flutter.io/) 爱好者使用。

如果你足够努力，你可能会找到用你最喜欢的语言创建一个 web 应用程序或微服务的方法。这样做总是可行的吗？不一定，但至少有可能！

# 技术正在发展并变得更加多样化

当我 2014 年从大学毕业时，我 90%的技能都与 Android、WPF 和 Java 服务相关。C#和 Java 真的是我所知道的全部。

回顾过去几年的职业生涯，我已经学会了许多不同的语言。我在这个 Github repo 中有我过去两年提交的所有内容:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[jvar ness](https://github.com/jvarness)/[cerner-2-5](https://github.com/jvarness/cerner-2-to-the-5th)

### 2^5 提交的材料

<article class="markdown-body entry-content container-lg" itemprop="text">

# cerner-2 的 5 次方

2^5.提交的材料

假设麻省理工学院许可所有提交。

</article>

[View on GitHub](https://github.com/jvarness/cerner-2-to-the-5th)

我从知道...真的只有两种语言，几年内就能学会十几种！

我现在知道的许多语言似乎都是在过去几年才真正受到重视的:Go、Dart 和 Elm 已经被积极开发了相当长的一段时间，并且在我毕业后每一种语言都变得越来越受欢迎。

即使我还没有真正用这些语言开发出成熟的应用程序，我至少有机会学习它们。参加像 2^5 这样的比赛给了我一个机会，让我的技能变得更加多样化。

感谢你花时间阅读这篇文章，我希望你们喜欢我今天分享的所有小片段和东西！如果你对我写的任何东西或我写的代码有疑问，请留言！