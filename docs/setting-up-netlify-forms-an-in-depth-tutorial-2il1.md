# 设置网络表单:深入教程

> 原文：<https://dev.to/couellet/setting-up-netlify-forms-an-in-depth-tutorial-2il1>

[![netlify-forms](img/3b9d8098815f0823c65445ecd90ab3e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LdfmjzPl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://snipcart.com/media/205513/netlify-forms.png)

我从事网页开发已经很长时间了。

多久了？如果我没记错的话，我在避开恐龙的时候建立了我的第一个 web 服务器。

虽然我的记忆有点模糊，但我清楚地记得我职业生涯早期的一项任务是为客户构建表单处理 CGI 脚本。从我卑微的出身开始，科技已经走过了漫长的道路。

现代网络技术，如 Jamstack 和静态网站的兴起经常引起人们对动态功能的关注。

但是我想向您保证:将表单集成到您的项目中不应该是一件麻烦的事情。

在这篇文章中，我想谈谈你应该添加到你的开发带上的一个特别有效的工具: [Netlify Forms。](https://www.netlify.com/products/forms/)T3】

我将深入探讨这个特性:

1.  网络生活形式基础
2.  表单定制
3.  垃圾邮件防护
4.  增强型网络表单提交
5.  表单通知

在获得所有技术信息之前，让我们添加一些背景信息。

## 我是如何发现网络生活形式的

在过去，我使用 Perl(一种非常灵活的语言)和库来简化表单创建，但是令人震惊的是，我经常不得不编写自定义代码来获取表单输入并将其发送给其他人。即使在我最终切换语言(ColdFusion FTW)之后，我仍然花了很多时间做基本的表单处理。

这就是为什么当我开始深入 Jamstack 时(几年前，我们称之为静态网站)，表单处理是我主要关心的问题。幸运的是，出现了许多支持处理表单输入的服务。

例如，我的博客利用了[forms spree](https://formspree.io/)。它有一个体面的免费层(每月 50 次提交)，这对我的网站很好。

然而，我在 Netlify 上主持，他们提供的许多服务之一是表单处理。如果你还不了解 Netlify，你应该马上研究一下。这个团队做了大量的工作来促进动态功能与现代静态、以前端为中心的架构的集成(他们还在 2015 年创造了术语“Jamstack”。)

点击这里阅读这篇文章的其余部分