# Node.js 的认知负荷比 Java 高

> 原文：<https://dev.to/grahamcox82/nodejs-has-a-higher-cognitive-load-than-java-2hi6>

首先，我要说我是 Java 的忠实用户，而不是 node。我确实喜欢 node，但我只是不怎么用它。

我还想说，完全有可能是我缺乏 node 方面的经验影响了我的想法。

我认为 Java 比 node 更容易使用。我知道这将是一个非常不受欢迎的观点，甚至人们会想我怎么会有这种想法。所以我的理由是。

假设我正在编写一个简单的单页应用程序。我将使用:

*   某种形式的 HTTP 服务器充当 REST/GraphQL/whatever 服务。
*   创建 React 应用程序来构建 UI(出于论证的原因，我喜欢它)
*   数据层的 Postgres。(见上文)
*   使用 selenium 的全栈端到端测试

在 Java 世界中，我可以建立一个允许我这样做的构建。非常简单，最终结果就是我执行`mvn clean install`来:

*   下载所有依赖项
*   构建后端应用程序
    *   编译所有代码
    *   运行所有单元测试
    *   运行静态检查-例如 checkstyle
    *   完全集成测试
    *   为应用程序构建 docker 映像
*   构建 webapp
    *   生成输出文件
    *   运行所有单元测试
    *   为 webapp 构建 Docker 映像
*   构建最终测试
    *   为测试构建一个 Docker 映像

然后，我可以一起或单独启动前端和后端，重要的是，我可以运行整个系统的 Docker compose 堆栈，包括端到端测试...

这里有趣的是集成测试。我可以启动服务器，使用一个库自动启动和停止数据库，自动管理数据库模式，然后针对它运行一系列测试。所有这些只是隐藏在一个标准构建命令后面的一件事，数百万其他项目也使用这个命令...

现在让我们考虑节点。

首先，据我所知，没有办法在 node 中进行多模块构建。这意味着我必须知道如何分别构建三个不同的模块。

第二，集成测试。问了一下周围的人，似乎都不喜欢使用一体化流程。不是用一个命令来运行测试，它会为你做所有的事情，通常期望你会:

*   自己启动所有相关服务——也许使用 Docker
*   启动服务来测试你自己——也许使用 docker
*   然后对这个进行测试
*   那你自己把它全拆了吧

要做到这一点需要付出很大的努力。

第三，执行多步构建是笨拙的。像 grunt 这样的工具可以使它变得更简单，但这只是增加了一层复杂性，而隐藏了另一层。或者，您可以使用 shell 脚本，但这不是跨平台的。

这只是一个例子，但是已经表明在 node 中运行一个构建比在 Java 中更复杂。许多人认为这些步骤都比 Java 版本简单得多，这是事实。但是事实上，你必须知道这一切，这只会让事情变得更加难以收拾。

这并不是说这一切都不好。这里有很多好东西。在节点编写代码中，很容易获得非常快的周期时间，一秒钟后单元测试结果闪现，重复。或者编写代码，让服务器稍后重新启动，然后重复。Java 只是需要更长的时间来实现同样的事情。然而，开发人员需要对这一点有更多的理解。

至少，在我看来是这样。我很可能错过了让所有这些变得更容易理解的东西。