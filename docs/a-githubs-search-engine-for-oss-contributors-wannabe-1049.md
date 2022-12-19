# 一个 Github 的搜索引擎，面向 OSS 贡献者的追随者

> 原文：<https://dev.to/gaels/a-githubs-search-engine-for-oss-contributors-wannabe-1049>

TLDR:这里是现场直播[这里是](https://first-contrib.surge.sh)，代码是[这里是](https://github.com/GaelS/first-contrib-app)

[![App](../Images/9685405a32c0b23c4745dc218b7b6057.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KdJ0s_x6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7uvn3nxnspq3emfw4sgo.png)

大家好，

对于初学者来说，有时很难找到好的项目和好的问题来做出第一个开源贡献。为了解决这个问题，我创建了一个由 Github 的 GraphQL API 支持的应用程序。

它就像一个搜索引擎，允许你通过关键字和语言查询所有的存储库，找到带有初学者友好标签的问题(到目前为止，我已经列出了 50 多个！).

Github 的 API 并不真的期望这种用例，在这种用例中，你想要搜索所有的库。因此，必须(有些仍然需要)找到调整和变通办法，以更好地处理一些特性，比如分页。

此外，为了在练习我的 CSS 技能时获得乐趣，我尝试实现了一个 80 年代的 vibes 设计，希望你会喜欢它或者更好，会想改进它！

这个 app 是用 react(引擎盖下的 preact)、React 路由器和 React Apollo 实现的。

不管你喜不喜欢，请随时给我你的反馈:)

谢谢，
盖尔

PS:下面一个小预告:)

[![Preview](../Images/d59a76500e6f4fa9c9750ed8abe01104.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3SHEXQK---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m673htjy3wygtjfegd3o.jpg)