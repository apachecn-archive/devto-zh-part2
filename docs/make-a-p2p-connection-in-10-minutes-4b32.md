# 10 分钟内建立 P2P 连接

> 原文：<https://dev.to/carloslfu/make-a-p2p-connection-in-10-minutes-4b32>

[![Distributed Network](img/8ebf2ce86c027b659dd9eb4142b9d0e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kU3J16VH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9galmhzvf6ysjit4mfzt.png)

这是一个关于如何进行 P2P 聊天的简短指南。我的目的是让你只是浅尝辄止，如果你愿意，我可以写一篇更深入的文章来解释所有这些事情是如何工作的。话虽如此，我们还是开始吧。

# 准备

你唯一需要的是最新的 NodeJS 和你最喜欢的编辑器，我的是 Visual Studio 代码。现在为我们的项目创建一个文件夹，打开一个命令行，用 npm init 初始化一个 npm repo，并安装一些我们需要的库:

```
npm i --save discovery-swarm dat-swarm-defaults portfinder get-port 
```

Enter fullscreen mode Exit fullscreen mode

# 我们的第一个 P2P 连接

让我们从一个简单的 P2P 连接开始，感谢 Node.js 社区的一些优秀的库，它太简单了: