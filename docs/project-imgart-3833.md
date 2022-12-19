# 伊加特项目

> 原文：<https://dev.to/talento90/project-imgart-3833>

在我学习围棋的过程中，我多次发现以下问题:`How do I struct GO applications?`，`There is any good project as example?`。
嗯，我也提出了这个问题！我发现的每个例子，他们只是简单地把所有东西放在同一个包里...所以我决定创建 IMGART 项目作为一个真实应用程序的例子。

IMGART consists 是一个基于过滤器和配置文件的图像处理 HTTP 服务:[https://github.com/talento90/imgart](https://github.com/talento90/imgart)

您可以在此项目中找到的功能列表:

*   HTTP API 中间件(日志记录、序列化、错误处理)
*   使用 Swagger 规范的文档
*   Dockerfiles(开发和生产)
*   多个数据库(Redis 用于图像缓存，MongoDB 用于概要文件)
*   正常关机
*   健康检查
*   使用 Delve、VSCode 和 Docker 进行调试
*   错误处理
*   生成文件

我正在考虑创建一个博客系列来解释我的决定...你怎么想呢?

我将感谢您的反馈(负面/正面)

快乐编码:)