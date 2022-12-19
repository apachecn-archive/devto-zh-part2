# 无服务器测试策略:测试 Node.js + AWS Lambda + API 网关应用程序

> 原文：<https://dev.to/didil/serverless-testing-strategies-4g92>

*我最初在[媒体](https://medium.com/@didil/serverless-testing-strategies-393bffb0eef8)T3 上发布了这篇文章*

无服务器计算和 FaaS(功能即服务)计划在未来几年大规模增长。而且每个主要的云提供商都已经有了产品:AWS Lambda、Google Cloud Functions、Azure Functions……但是这对我们网络开发者意味着什么呢？当从传统的基于服务器的应用程序转移到“无服务器”时，我们如何调整我们的开发工作流程？让我们探索一下这个故事的测试部分！

[![AWS Lambda was first introduced in November 2014](../Images/50839023242cdd0296de91e59a94b38c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--68KzFMa5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A2AaJTMWh6pu5DiroIT9kIg.png)*2014 年 11 月首次推出 AWS Lambda*

每当我尝试一项新技术时，首先出现的问题之一是:**我如何编写自动化测试？我认为测试是任何软件项目的一个非常重要的方面。毕竟，如果一个软件不能被容易地测试，那么它怎么能被维护呢？**

幸运的是，有一些方法可以测试无服务应用。对于本文，我们将构建一个 Node.js 无服务器应用程序，我们将使用[无服务器框架](https://serverless.com/)和 [mocha.js](https://mochajs.org/) 来编写和运行我们的测试。如果你在阅读本文时想浏览代码，可以使用我准备的 [github 库](https://github.com/didil/serverless-testing-examples)。

## 一个简单的λ函数

为了给无服务器测试提供一些模式，我们将构建一个简单的 lambda 函数“asyncConcat ”,它接受两个字符串参数，将它们连接在一起并返回结果。我们还将使用 AWS API Gateway 构建一个相应的 API 端点。我们还将为这些组件编写单元/集成测试。这是我们将要构建的流程图:

[![Request/Response cycle for asyncConcat](../Images/ad0063ff0d5c83de5131fea8bf0b6343.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IEteOLjk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A-wk8xOPkgCOe7pyHxQrslg.png)*async concat*请求/响应周期

## 代码

我们将使用自顶向下的方法，首先在 serverless.yml 文件中定义 http GET /asyncConcat 端点