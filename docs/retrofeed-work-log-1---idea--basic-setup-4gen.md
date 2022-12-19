# 反馈工作日志#1 -想法和基本设置

> 原文：<https://dev.to/horia141/retrofeed-work-log-1---idea--basic-setup-4gen>

上个月，我开始了一项在雷诺 RPC 上记录我的工作的实验( [log1](https://dev.to/horia141/raynor-rpc-work-log-1-4if0-temp-slug-4364995) 和 [log2](https://dev.to/horia141/raynor-rpc-work-log-2-34b6-temp-slug-1126544) )。虽然在图书馆工作很有趣，但是有一个更清晰的方向会更好。比如某*产品*提供的。所以在这篇文章中，我改变了一些方式，开始写一个我想做的小项目，其中我将集成 Raynor RPC。在任何情况下，就像 Raynor RPC 一样，公开谈论这件事至少有助于让我继续致力于这个项目。即使观众是我和 Googlebot。在最好的情况下，也许会出现一个真实的东西，有用户、发行版、bug 和愤怒的 GitHub 线程。在任何情况下，也许这里的信息将证明对人们有用，不管这将走向何方。

在这篇文章的其余部分，我将介绍这个想法本身，对所选的*技术栈*的快速概述，以及对初始开发步骤的描述。

我先来个剧透——这个项目是衍生的。它类似于 [dependencies.io](https://www.dependencies.io/) 或[requires . io](https://requires.io/)——一个跟踪项目依赖关系并通知你最新变化、安全问题等的工具。这是一个我已经考虑了一段时间的项目，但是从来没有抽出时间去做。我渴望对它有适当的 RSS 提要支持，以及对报告内容的各种控制。因此，及时了解依赖关系的变化非常适合我的工作流程。最后，我要把这个东西叫做*反馈*。如上所述，这并不是什么新鲜事。所以建造它的主要理由只是“这是我的步枪，有很多像它一样的…”

好了，现在来点有趣的。软件栈将会是 Node 和 TypeScript 作为后端，NestJs 和 Express 作为 web 框架，Raynor 和 Raynor RPC 作为 NestJs 的 RPC 类型的东西。我将使用 Postgres 作为数据存储，Redis 用于缓存。我将使用 Knex.js 作为数据访问库。我将使用 React &它是用于前端的生态系统。本地环境将在 Docker 上运行，使用 docker-compose。部署将在谷歌的云上完成。我将使用托管 SQL 和 Redis 产品，并在生产中运行 Docker。但是我不会走 Kubernetes 的路线，而是简单地在一个带有`docker run`的计算节点上运行服务。当然，基础设施是通过 terraform 的代码。我将使用的其他服务是代码托管 GitHub，Travis。Ci 用于构建，NPM 用于包托管，Codecov.io 用于覆盖报告，Cypress.io 用于端到端测试，Auth0 用于轻松认证，GitHub 的 API 用于存储库 *stuff* 。代码将会在麻省理工学院的许可下发布，我会尽可能的公开我正在做的事情。

就*应用架构*而言，它将一直是*独石*。所有代码都在一个存储库中。同上，就有争议的选择而言，我会尽量避免完全水疗。如果我真的到了这种地步，我肯定会后悔，并写下强制性的“RetroFeed 是如何从他们的老产品转向微服务/无服务器等”的文章。

无论如何，我会长话短说，到此为止。下一篇文章将讨论如何为一个`"hello world"`应用程序做准备。这比你现在想象的要复杂得多，所以请继续关注。

点击查看系列[中的所有文章。](//https//horia141.com/retrofeed.html)

* * *

[1]这可能会采取什么形式，我现在还不能 100%确定。但是，特别是对于 React，我觉得对于大多数项目来说，走 SPA 路线并不容易。