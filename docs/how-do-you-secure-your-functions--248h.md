# 你如何保护你的功能？

> 原文：<https://dev.to/jsitapara/how-do-you-secure-your-functions--248h>

我正在做一个项目，概述如何保护你的功能？你的经历是什么？或者说你是怎么做到的？或者任何你实验过的东西？

我试图使用 AWS API Gateway 和 AWS Lambda 在我的无服务器 REST API 服务中创建一个受保护的 API 端点。

关于“保护功能”的类似问题建议在中间件服务器上创建一个包装器 API，它实际上使用有效的 API 键进行受保护的调用，这显然违背了无服务器的目的。