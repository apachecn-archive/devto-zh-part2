# 在编写 FaaS 函数时，应该考虑供应商锁定吗？

> 原文：<https://dev.to/paulswail/should-you-be-concerned-about-vendor-lock-in-when-writing-faas-functions-2401>

供应商锁定是一个自云计算早期以来就被详细讨论过的话题，并且在今天的无服务器应用环境中尤为普遍。

> “Lambda 和无服务器是我们见过的最糟糕的专有锁定形式之一”
> —core OS 首席执行官(来源: [The Register](https://www.theregister.co.uk/2017/11/06/coreos_kubernetes_v_world/)

但是，在选择功能即服务(FaaS)提供商时，这真的是您需要关心的问题吗？

或者仅仅是竞争厂商和“传统的”软件架构师反对学习新的范例而制造的恐慌？

在这篇文章中，我们将浏览 3 个主要云提供商(AWS Lambda、Google Cloud Functions 和 Microsoft Azure Functions)的 FaaS 产品，并研究如何切换提供商。

## 你为什么会想转行？

首先，让我们看看您可能想要更换 FaaS 提供商的一些潜在原因:

*   您希望将您的功能与另一个提供商提供的新后端服务集成在一起
*   当前提供程序遇到运行时问题或限制
*   功能执行变得过于昂贵，而在其他地方会便宜得多
*   提供程序不再支持您的语言运行时
*   组织战略或支持原因

## 切换会涉及到什么？

在衡量转换 FaaS 提供商的努力方面，我们将考察:

*   需要对功能代码库进行更改。
*   对执行配置、部署和触发器的更改。

### 代码修改

每个云提供商都指定了一个函数签名，您的代码必须遵守该签名才能在他们的云中执行。每个提供商的签名略有不同。下面的 Node.js 代码示例展示了如何为每个提供者从触发事件中读取输入并发回响应。每个例子都假设它是由 HTTP 请求触发的。

#### AWS Lambda ( [docs](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-handler.html)

```
exports.myHandler = async (event, context) => {
    const input = JSON.parse(event.body);
    return {
        statusCode: 200,
        body: {
            message: `Hello ${input.myName}`
        }
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### Azure 函数([文档](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node)

```
module.exports = (context, req) => {
    const input = JSON.parse(req.body);
    context.res = {
        status: 200,
        body: {
            message: `Hello ${input.myName}`
        }
    };
    context.done();
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 谷歌云功能([文档](https://cloud.google.com/functions/docs/quickstart)

```
exports.myHandler = (req, res) => {
    res.status(200).send({
       message: `Hello ${req.body.myName}`
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

从上面的 3 个例子中可以看出，每个提供者之间的差异很小，所以重构它们应该不是一件很大的工作。像[云事件](https://cloudevents.io/)(旨在标准化触发功能的事件的模式)这样的倡议，应该有助于在未来进一步减少这些差异。

另一点需要考虑的是云提供商支持的所选语言的运行时，特别是如果您正在使用新的语言功能。例如，在编写 AWS Lambda 时，它支持 Node.js v8.10，该版本具有新的 async/await JavaScript 语法。但是 Google Cloud functions 最高只支持 v6.14 不支持这个功能。

### 部署、触发和执行配置的变更

每个提供商提供不同的设置，允许您配置您的功能将如何执行，特别是关于它们如何被触发以及应该为它们分配什么计算资源(内存、CPU)。对于部署，每个提供者都有自己的 CLI 用于打包和发布功能。因此，如果您选择使用这个 CLI，您的部署脚本将需要更新。
关于触发器，在撰写本文时需要注意的主要区别是，要通过 HTTPS 调用 AWS Lambda 函数，您需要使用 [API 网关服务](https://aws.amazon.com/api-gateway/)。这将涉及到设置自己的配置，而且也是单独收费的。另一方面，Azure 函数和 Google Cloud 函数原生支持 HTTP 触发器，无需额外费用。

## 您如何降低未来需要切换的风险？

以下建议将有助于您设计 FaaS 解决方案，从而最大限度地减少将来将您的功能迁移到不同的 FaaS 提供商所需的工作:

*   检查您的组织的首选语言运行时是否得到多个提供商的支持。
*   不要通过多个助手函数传递特定于提供者的参数(例如`context`或`res`)。相反，从主处理函数的参数对象中解析出您需要的字段，并传递这些字段。
*   将对特定于提供商的服务(例如 AWS S3)的任何调用提取到它们自己的帮助器函数/模块中，以便您可以在将来轻松地交换这些调用。
*   为每个处理函数维护本地可运行的单元测试，作为代码重构的安全网。
*   检查您的应用程序所需的支持触发器(例如 HTTP 请求、队列消息、CRON 计划)在其他云提供商中是否有对等项。
*   检查您的提供商施加的限制(例如，内存分配和最大请求超时)是否在您的应用程序的要求范围内，并将这些限制与其他云提供商进行比较。例如，在编写本文时，AWS Lambda 支持高达 3GB 的内存分配和 5 分钟的最大超时，而 Azure 支持最大 1.5GB 的内存，但最大超时为 10 分钟。
*   使用[无服务器框架](https://serverless.com)来打包和部署你的应用。它支持所有主要的云提供商，意味着您可以在单个文件(`serverless.yml`)中掌握您的功能和触发配置，并使用相同的 CLI 命令部署到每个提供商。可以从开发人员计算机或 CI 管道中调用此 CLI。

## FaaS 以外的厂商锁定

在本文中，我们讨论了无服务器架构的 FaaS 组件。实际上，我不认为对于绝大多数组织来说，就代码和功能配置以及构建和部署过程所需的更改而言，供应商锁定应该是一个主要问题，特别是如果注意了上述建议的话。

然而，就供应商锁定更广泛的无服务器架构而言，更大的问题是您的功能所集成的服务。您的应用程序不可避免地需要在关系或非关系数据库、文件存储、缓存或队列中存储数据。三大供应商在这些领域提供了无数的服务，都有不同程度的锁定。在做出这些架构决策时，有许多权衡要考虑，我希望在以后的文章中更详细地探讨这些权衡。

💌 ***如果你喜欢这篇文章，你可以注册[到我关于在 AWS](https://winterwindsoftware.com/newsletter/) 中构建无服务器应用的每周时事通讯。***