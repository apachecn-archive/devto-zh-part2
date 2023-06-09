# 世界积木创造者

> 原文：<https://dev.to/justinctlam/world-block-creator-3b4p>

# 我造了什么

[![](img/310437d6a7c32b7ed2b09cf501b21062.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--65BVbcVq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0pe4mkgd797rn9y09fcb.png)

这个在线网络应用程序允许你用积木搭建一个 3D 世界。当你在构建自己的世界时，你也可以看到其他人在实时构建他们的世界。此外，你可以进入旁观者模式，看到你周围发生的所有创造。从他人那里获得灵感，并构建自己的灵感来展示。

## 演示链接

[http://worldblockcreator.herokuapp.com/](http://worldblockcreator.herokuapp.com/)

## 链接到代码

[https://github.com/justinctlam/WorldBlockCreator](https://github.com/justinctlam/WorldBlockCreator)

# 我是如何建造的

**堆栈概述:**
推送器-[https://pusher.com/](https://pusher.com/)T5】React-[https://reactjs.org/](https://reactjs.org/)
Babylon . js-[http://www.babylonjs.com/](http://www.babylonjs.com/)
Heroku-[https://www.heroku.com](https://www.heroku.com)
TypeScript-[https://www.typescriptlang.org/](https://www.typescriptlang.org/)
node . js-[https://nodejs.org/en/](https://nodejs.org/en/)

作为 web 堆栈开发领域的新手，我必须学习很多关于如何从头开始开发一个简单的 web 应用程序的知识。我知道我想使用 TypeScript 和 Babylon.js，因为这是我最近学到的东西。

我的第一步是创建一个 hello world 客户机和服务器应用程序，并部署它以供使用。我在这方面有点纠结，大多数教程都没有给出所有的小细节。一些教程是我想要的堆栈的组合，但是没有一个教程能满足我所有的需求。然而，我确实找到了一个，我最终用它作为我的应用程序的基础。[将 React 和 Express 部署到 Heroku](https://daveceddia.com/deploy-react-express-app-heroku/) 是我发现的最好的一个，它教会了我如何在本地使用 React 创建客户端和服务器应用程序，以及如何在 Heroku 上部署以进行分发。它为我创造了奇迹。

我想用 TypeScript 代替 JavaScript，因为我发现类型检查非常有用。我决定用微软的 [TypeScript-React-Starter](https://github.com/Microsoft/TypeScript-React-Starter) 工具包。如果你正在遵循从 [Deploy React 和 Express 到 Heroku](https://daveceddia.com/deploy-react-express-app-heroku/) 的教程，你可以从`create-react-app client`到`create-react-app client --scripts-version=react-scripts-ts`做一个简单的改变，在应用中启用打字。

您还可以通过将 TypeScript 包添加到 serve 应用程序来在服务器端添加 TypeScript 支持。你可以在我的 GitHub 上找到一个例子。

从这里开始，我使用 React 和 TypeScript 得到了一个完全可用的应用程序和服务器，它可以在本地测试，也可以在 Heroku 上部署！

对于我的 Pusher 竞赛想法，我想做一些 3D 的东西，所以我决定使用 Babylon.js 作为我的 3D 框架/引擎包。我知道 Three.js 也很受欢迎，但我一直在学习和使用 Babylon.js，最好使用我目前最有效的方法。我想做一些用户可以创建和分享的东西，我会用 Pusher 让你实时看到别人在做什么。

基本概念很简单。向用户呈现一个地平面，当用户点击它时，在用户点击的地方出现一个小框。用户可以继续点击平面或者开始点击方框，并且更多的方框被放置在用户点击的地方。用户也可以通过右键单击来移除框。从那里，用户可以为盒子选择不同的颜色，并开始构建他们所能想象的任何东西。

当用户决定休息一下时，他们可以自由地进入旁观者模式，飞来飞去实时观察其他用户构建他们的世界。从他人的设计中获得灵感，并将这些设计融入到自己的设计中。

对于应用程序的实时方面，我使用推送通道在客户端和服务器之间进行通信。当用户在客户端做一些事情时，比如添加一个框或者删除一个框，一个事件被发送到服务器。在服务器端，这些事件被转发给连接到服务器的其他客户端。同样在服务器端，我使用 Pusher 的 webhooks 来确定客户端何时断开连接。则服务器将通知所有其他客户端关于该断开的客户端，并且客户端将从 3D 空间中移除与该断开的客户端相关联的元素。

**未来考虑事项**

因为我在这个应用上花费的时间有限，所以有几个功能我还没有实现。我希望将来能接触到他们:

*   将数据存储在数据库中，以便在会话之间保存
*   考虑身份验证以允许用户登录
*   允许用户发送关于设计的赞
*   添加更多工具或形状以实现更具表现力的设计

# 附加资源/信息

我希望听到任何反馈、错误或功能需求。

对于 Pusher 团队来说，拥有对服务器 API 的 TypeScript 支持是件好事。