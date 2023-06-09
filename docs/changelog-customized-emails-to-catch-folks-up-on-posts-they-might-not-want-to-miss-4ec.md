# Changelog:定制的电子邮件，让人们了解他们不想错过的帖子

> 原文：<https://dev.to/ben/changelog-customized-emails-to-catch-folks-up-on-posts-they-might-not-want-to-miss-4ec>

我们创建了一种新型的电子邮件，名为 **DEV Digest** ，它将根据人们关注的标签，帮助人们了解平台上发生的一些更有趣的事情。它更多的是面向偶然的[开发者到](https://dev.to/)读者，而不是那些每天自己回来的人。在您的[设置](https://dev.to/settings/notifications)中，您可以随意关闭这封邮件。

## 多长时间发出一次？

这封电子邮件会“时常”发出，这取决于每个成员如何看待它。基本上，如果人们已经通过他们的行动表明他们并不那么喜欢它，我们使用一种[指数补偿](https://en.wikipedia.org/wiki/Exponential_backoff)算法来减少发送频率。

这将有助于人们了解自上次电子邮件以来他们可能错过的事情。人们喜欢通过各种方式了解最新消息，这应该可以服务于社区中依赖电子邮件的大部分人。

Mac 主要负责这项功能，并在构建这类事情所需的所有复杂逻辑方面做了大量工作。