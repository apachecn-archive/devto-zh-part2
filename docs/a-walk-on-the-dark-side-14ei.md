# 黑暗面上的行走

> 原文：<https://dev.to/hector6872/a-walk-on-the-dark-side-14ei>

## 出现了觉醒……

[![](../Images/351c65be1b00ab790524d66e556e75b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yn3zY0xb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/hrules6872/Writings/master/7-dark-side/art/1.jpeg)

## 简介

今年夏天[我们的技术负责人](https://twitter.com/jberlana)告诉我们，我们被允许花一定比例的时间[在工作中](https://lolamarket.com/)做一些我们不习惯做的事情或者研究一些我们不习惯做的事情([一种谷歌的“20%时间政策”](https://www.businessinsider.com/google-20-percent-time-policy-2015-4?IR=T)……但是是以好的方式)。只有一个条件:我们必须就选定的主题给团队的其他成员做一次技术报告。

教学是最好的学习方式。

## 动机

因为我经常取笑队友在日常工作中使用的编程语言(100% trolling 只是为了好玩我发誓；我不想挑起任何无聊的编程语言战争(T1)。我想用一种我已经很久没有接触过的语言来回报他们会很棒: **[PHP](https://eev.ee/blog/2012/04/09/php-a-fractal-of-bad-design/)** 。

## 想法

我是一个真正的自动化爱好者([即使我们花在编码](https://xkcd.com/1205/)事情上的时间比它实际上为我们节省的时间还要多)，所以基于一个我们几乎每天都在工作中使用的功能，被称为“[通过提交消息自动关闭问题](https://confluence.atlassian.com/bitbucket/resolve-issues-automatically-when-users-push-code-221451126.html)”(我猜所有基于 web 的 Git 库管理人员都有一个好听的名字)，我开始编码**一个 [Bitbucket](https://medium.com/@hector6872/moving-from-github-to-bitbucket-30c12dd8aea5) Webhook。**

## Webho……什么？

一个 **[WebHook](https://en.wikipedia.org/wiki/Webhook) 是一个`HTTP`回调**:一个实现 WebHook 的 web 应用会`POST`一个消息(有效载荷)给一个用户定义的`URL` (web 服务)当事情发生时。就这么简单。

所以，在我的例子中，我编码的是一个 [Githook](https://medium.com/@hector6872/how-good-are-git-hooks-75ae4c3fe3f5) 但是另一端的(基本上它连接了 Bitbucket+ [Trello](https://developers.trello.com/) )。

我认为不值得给出关于我的项目本身的更多细节，但我可以总结以下几点:

*   团队中有人推动[回购](https://confluence.atlassian.com/bitbucket/manage-webhooks-735643732.html)

*   Bitbucket 向我们的 webservice 发送一个[有效负载](https://confluence.atlassian.com/bitbucket/event-payloads-740262817.html)

*   它解析这些信息，然后:**做我们能想象的任何事情。**

## 结论

走出我的[舒适区](http://lmgtfy.com/?q=comfort+zone+where+the+magic+happens)非常有趣(我不知道我专门为 [Android](https://play.google.com/store/apps/details?id=com.comprea.client) 平台编写代码有多少年了)，我可以确认我利用了我们工作中的大好机会(我希望每个人都有):**我学到了很多**(现在我能够更好地驾驭它们🤷).

PS: [我们要招人了！](https://lolamarket.com/jobs/)T3】

## 免责声明

没有幻灯片(我把我的技术演讲分成:代码审查，我的队友指出了我所有的错误进行报复😃&一个现场演示)或公共存储库(我不敢公开它，因为我知道它没有我希望的那么好)但是我团队中的一个人(他是一个真正的 PHP 绝地武士)认为这个想法可以改进(我认为这是一个惊人的提议，因为我们仍然可以学习它),所以也许将来我们会在这个基础上发布一些东西。

**[敬请期待！](https://twitter.com/LolaMarket_tech)T3】**

本文最初发表于[媒体](https://medium.com/@hector6872/a-walk-on-the-dark-side-669213cc2260)

* * *

[1]尽管这可能是一个简单的脚本，但我要求自己满足一些要求:既不使用任何类型的框架也不使用任何类型的库，遵循良好的实践、模式和 SOLID，并添加一些测试( [PHPUnit](https://phpunit.de/) is love，my friends)。

* * *

[外部链接👀](https://gist.github.com/hrules6872/25fcd2ab507f2293c8ab8720983afcc4)