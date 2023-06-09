# 永远运行 node.js

> 原文：<https://dev.to/chancesm/running-nodejs-forever-3eof>

# 简介

NodeJS 是一个很好的 web 开发工具，但是它也有自己的缺点。当用 NodeJS 设置 Express 服务器时，在开发阶段需要并预期会有许多重新启动。不仅如此，一旦您关闭了终端会话，让 NodeJS 项目无限期地运行也是一件好事。本教程旨在解决这两个问题。本教程将首先解决保持 nodeJS 流程运行的需求，然后解决在发生更改时重新加载流程的问题。为此，我将介绍用于完成这些任务的不同工具，并以我的首选结束。我们开始吧。

注意:这是从 WORDPRESS 博客上复制的，因此一些样式和文本没有被正确的转换

* * *

# 视频(给所有非读者)

[https://www.youtube.com/embed/7fpSFwwNqYA](https://www.youtube.com/embed/7fpSFwwNqYA)

* * *

# 选项:

## Tmux/Screen(ok 选项)

Tmux 和 Screen 是终端多路复用器，允许您创建“会话”,这些会话可以在您关闭终端后持续存在。它们创建自己的套接字，终端实例在其中运行。然后，用户可以通过他们工具的命令来访问它。一旦进入这些会话之一，使用 node 运行命令然后离开会话将不会结束节点进程，因为终端会话仍处于打开状态。我不会在这里详细介绍，但是如果你看了视频，我会展示如何用 tmux 做到这一点。

## Nodemon(更好的选项)

上述方法的一个问题是，每次对服务器进行更改时，仍然需要停止并重新启动节点进程。解决这个问题的一个办法是安装 nodemon，这是一个 nodejs 包装器，它监视运行它的目录中的文件并重新启动。Nodemon 是一个节点模块，可以与 npm 一起安装。将 nodemon 与 tmux 或 screen 结合起来将允许您运行一个节点服务器，只要发生变化，它就会重新启动。有一个更简单的方法。

## PM2(最佳选项)

PM2 是 NodeJS 的流程经理。和 nodemon 一样，它也是通过 npm 全局安装的。与 nodemon 不同，pm2 实际上创建了一个独立于任何终端实例的系统进程来运行您的节点进程。视频显示了如何安装后设置它，所以我不会在这里讨论它。他们的网站上有一些很棒的文档。

* * *

# 结论

有不同的方法来完成任务运行节点永远，但我最喜欢的，因为我第一次开始使用它，是 PM2。它允许用户监控多个节点进程，观察文件的变化，甚至以集群模式运行以实现负载平衡。