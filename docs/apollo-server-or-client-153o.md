# 阿波罗服务器还是客户端？

> 原文：<https://dev.to/kurisutofu/apollo-server-or-client-153o>

我需要为我创办的公司创建一套网络工具。由于这是我的公司，我是决定使用何种技术的人，而且由于目前这只是副业，我不急着选择(在范围之外)。

我已经很久没有编程了。net 和 C#或者用 jQuery 的基础 javascript(有人记得这个吗？:D)但是我曾经有机会用 PhoneGap 玩 Node.js，这让我想尝试更多。

我最近也尝试了 Graphql，我喜欢它，所以现在我想使用它。

在阅读了许多关于 **Node.js** 和 **Graphql** 以及围绕它们的所有解决方案之后，我得出结论，我想创建一个结合了服务器端的 **Graphql + Apollo Server** 和客户端的 **Apollo Client + Vue.js** 的解决方案。

我认为服务器端是一个 API，也许以后会在移动设备应用程序中使用它，但那是以后的事了。

[![Graph](img/028062d55be5ae924d6583984844bc7d.png "What I'm thinking of doing")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EAhkVkjz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uxqscxzwwgs3g5g5aost.png)

作为测试，我可以用 **graphql** 和 **Apollo Server** 创建一个快速 API，但是现在我需要创建客户端，我有点不知道如何实现它。

我在想...我真的需要阿波罗的两个版本吗？
我喜欢 API 的灵活性，但是你会推荐简单的使用**Apollo Client+vue . js+graph QL**吗？

此外，在我目前的计划中，如果我不直接使用 **Graphql** ，我会失去 **Apollo 客户端**的好处吗？