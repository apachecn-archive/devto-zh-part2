# 发现知识并不难。把这些放在一起。

> 原文：<https://dev.to/topheman/finding-knowledge-is-not-hard-putting-it-all-together-is-33ol>

## 我做 top Heman/NPM-注册表-浏览器的原因

在软件开发中，很多高质量的资源都是可用的，而且通常是免费的。我在工作中、在网上、在聚会上从开发者那里得到反馈，他们分享说**困难的部分不是找到知识**，而是**选择一个库或者把它们放在一起**。

解释一个特定问题的教程到处都是，缺少的是具有**更广视角**的项目示例/课程。

这是我在最新的项目中决定要做的:[top Heman/NPM-registry-browser](https://github.com/topheman/npm-registry-browser)。我尊重您在开发真实应用程序时会遇到的一些限制，例如:

*   外部 API 调用
*   使用外部库(UI 套件、路由器、http 客户端……)
*   团队开发的项目设置
*   代码质量(代码格式)
*   测试(单元/端到端)
*   自动化/开发渠道

该项目本身是一个单页应用程序，允许您在 npm 注册表中搜索软件包，并显示每个软件包的详细信息，如自述文件、版本、统计数据……事实上，**该项目只是一个借口，以展示如何将我上面提到的所有技术**整合在一起。

源代码可以在 [github](https://github.com/topheman/npm-registry-browser) 上获得。你可以在线测试一个[演示](https://topheman.github.io/npm-registry-browser)。我将在接下来的几周内添加更多的功能。

[试试看](https://topheman.github.io/npm-registry-browser)

**PS:** 该项目基于 [create-react-app](https://github.com/facebook/create-react-app) ，保留**未注入**。这是我从一开始就强加给自己的约束。我以前从未使用过 CRA(我有自己的 [webpack starter-kit](https://github.com/topheman/webpack-babel-starter) )，所以我想测试一下它，看看它能做什么，不能做什么。

我选择不使用 Redux，至少在第一个版本中没有，因为……[你可能不需要 Redux](https://github.com/topheman/npm-registry-browser#why-not-use-redux-) (解释)……

📺[观看演讲视频(fr)](http://dev.topheman.com/pourquoi-realiser-topheman-npm-registry-browser-video-talk/)

这篇文章最初发表在我的博客上。