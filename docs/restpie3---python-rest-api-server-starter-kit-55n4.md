# RESTPie3 - Python REST API 服务器初学者工具包

> 原文：<https://dev.to/tomimickelsson/restpie3---python-rest-api-server-starter-kit-55n4>

你好 DEV 社区！我在这里的第一篇文章。

我是一名 python 老手，已经用各种 python 框架构建了许多 web 服务很长时间了。近年来，我通常开始使用的代码库已经稳定为一组连贯的源文件和组件，我最近对它们进行了开源。

RESTPie3 是一个实用而灵活的入门代码库，它在一个漂亮的包中提供了基本的 REST 服务特性:主守护进程、数据库、会话、缓存、队列、后台工作器、认证、授权、cron、日志记录。您可以将服务对接到云中，或者简单地通过 rsync 部署到 vps 中。

RESTPie3 依赖于一些可靠的组件，如 Flask、uWSGI、PostgreSQL 和 Redis，这些组件多年来为我提供了很好的服务。在这个初学者中没有什么特别的，只是在我看来，这是一个简单而明智的构建 REST 服务的方法。

此外，在这个 starter 中没有前端，因为它旨在作为一个纯粹的 REST 服务器。然而，我还在 Vue 和 React 中编写了与 REST 服务器对话的示例 SPA 前端。

Github README 上的完整文档。去叉吧。

## ![GitHub logo](../Images/a73f630113876d78cff79f59c2125b24.png) [托米米克](https://github.com/tomimick) / [安息 3](https://github.com/tomimick/restpie3)

### Python3 REST API 服务器初学者工具包

<article class="markdown-body entry-content container-lg" itemprop="text">

# RESTPie3 - Python REST API 服务器初学者工具包

这是一个用 Python3 实现的轻量级 REST API 服务器，在一个简单的包中提供了基本的 web 服务特性。这不仅仅是一个框架，而是一个实用而干净的代码库，它依赖于几个核心组件来很好地完成工作。快速分叉并创建自己的 REST API 服务器。

经过多年在多个站点的生产使用后，于 2018 年 9 月开源。

**目录**

*   [特性](https://raw.githubusercontent.com/tomimick/restpie3/master/#features)
*   [积木](https://raw.githubusercontent.com/tomimick/restpie3/master/#building-blocks)
*   前端呢？
*   [源文件](https://raw.githubusercontent.com/tomimick/restpie3/master/#source-files)
*   [使用 Docker](https://raw.githubusercontent.com/tomimick/restpie3/master/#run-locally-with-docker) 在本地运行
*   [与 Docker](https://raw.githubusercontent.com/tomimick/restpie3/master/#develop-locally-with-docker) 在本地开发
*   [API 方法](https://raw.githubusercontent.com/tomimick/restpie3/master/#api-methods)
*   [认证&授权](https://raw.githubusercontent.com/tomimick/restpie3/master/#authentication--authorization)
*   [会话数据](https://raw.githubusercontent.com/tomimick/restpie3/master/#session-data)
*   [再编阵列](https://raw.githubusercontent.com/tomimick/restpie3/master/#redis-storage)
*   [后台工作人员&克朗](https://raw.githubusercontent.com/tomimick/restpie3/master/#background-workers--cron)
*   [测井](https://raw.githubusercontent.com/tomimick/restpie3/master/#logging)
*   [测试](https://raw.githubusercontent.com/tomimick/restpie3/master/#tests)
*   [部署到 VPS](https://raw.githubusercontent.com/tomimick/restpie3/master/#deploy-to-vps)
*   [设置 VPS 服务器](https://raw.githubusercontent.com/tomimick/restpie3/master/#setup-vps-server)
*   [Nginx](https://raw.githubusercontent.com/tomimick/restpie3/master/#nginx)
*   [安全](https://raw.githubusercontent.com/tomimick/restpie3/master/#security)
*   [放大](https://raw.githubusercontent.com/tomimick/restpie3/master/#scaling-up)
*   [需要帮助？](https://raw.githubusercontent.com/tomimick/restpie3/master/#need-help)
*   [执照](https://raw.githubusercontent.com/tomimick/restpie3/master/#license)
*   [截图](https://raw.githubusercontent.com/tomimick/restpie3/master/#screenshot)

## 特征

这个 Python API 服务器的功能的快速列表:

*   简单灵活的服务器，依赖性最小
*   基于流程的请求工作线程，而不是基于线程或异步的
*   安全的服务器端会话与…

</article>

[View on GitHub](https://github.com/tomimick/restpie3)