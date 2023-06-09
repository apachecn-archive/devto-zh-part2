# ReactJS 和无服务器架构的最小可行产品部署

> 原文：<https://dev.to/joshichinmay/minimum-viable-product-rollout-with-reactjs-and-serverless-architecture-2phj>

最近，我加入了一个新组织， [Supahands](http://supahands.com) ！
作为我的第一个任务，我应该为我们的一个产品评估一些前端框架。对我来说，这是一项具有挑战性的任务，我在这个过程中学到了很多。

[![yaay](img/756ed2406e1513f3cd087604cd8dcec8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GlPcxV0U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e0gii478k3kyj8oc0cnf.gif)

* * *

对于我们的新产品，我们心中有四个框架。**反应/ Vue /角度/余烬。**
经过几天的评估，我们得出的结论是 **ReactJS** 非常合适。

该产品的目标很容易实现。这是一个简单的 CRUD 操作应用程序，通过一些服务调用来检索一些数据。我们已经有了一个使用 Ruby On Rails + PostgreSQL 堆栈的运行产品。
在检查了我们的目标后，我们意识到我们需要适应更好的技术和架构。

**我们试图实现以下目标:**

*   将我们的业务逻辑转移到独立服务(AWS Lambda)。
*   为应用程序引入新的 UI 设计和模板系统。
*   在 React 中编写客户端应用程序。
*   重构我们当前的数据库并迁移到 DynamoDB。
*   借助 AWS CloudFormation 实现 CI 流程和当前部署战略的自动化。我们的业务逻辑与部署堆栈紧密耦合。

我们为什么要这么做？

*   以降低成本。
*   在其他产品中重用服务。
*   拥有客户端单页应用程序。
*   与多个应用程序维护和共享一个公共数据库。

* * *

### 和...我们达到目标了吗？**是的！**

[![woohoo](img/16e52f04b1824074f1f1b3c1357b75f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HKyHKkXp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pzj1qescolcc7pb0zm2h.gif)

* * *

**在这个过程中我做了什么？**

*   我参与了 app 的 UI 设计过程。在 Bootstrap 的帮助下，我将这些设计转换成了 JSX 模板。
*   我成功地用 React 为我们的客户端应用程序编写了代码(从头开始)。
*   我将我们的 React 应用程序部署到 AWS。
*   让自己适应无服务器架构。而且，还在学习...
*   我用 Python 3.6 为 AWS Lambda 编写了后端服务。
*   我在 AWS DynamoDB 中设计了一个更好的数据库结构。

为什么会有挑战性？

*   我第一次与 React 和 AWS 合作。
*   我不知道无服务器技术是如何工作的。
*   从零开始到上线，我从来没有自己做过前端框架。

那么，下一步是什么？

*   改进应用程序的代码库。
*   为前端 app 添加测试用例。
*   添加 Redux 进行状态管理。
*   让应用程序对移动设备友好。

* * *

特别感谢**开发商**和**社区**。我为 React 和无服务器开发引用了很多线程！

[![Thank you](img/5129951ec09394029c0d468236606bdf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fOfIj6cz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s7ndt66xxefe0nhkauld.gif)

* * *