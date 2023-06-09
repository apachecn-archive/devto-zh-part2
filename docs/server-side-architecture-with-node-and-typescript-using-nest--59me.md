# 具有使用嵌套的节点和类型脚本的服务器端架构

> 原文：<https://dev.to/samueleresca/server-side-architecture-with-node-and-typescript-using-nest--59me>

*原贴于[https://samueleresca.com](https://samueleresca.net/)T3】*

每个平台都有自己的哲学:一套原则和指导方针。Node.js 受到了 Unix 哲学的强烈影响。特别是它的两项工作原则，即:

“小即是美。”

“让每个程序做好一件事。”

Node.js 核心的简单性及其模块化带来了很多优势:首先是可重用性。其次，模块变得更容易理解和使用，更容易测试和维护。当您想要**遵循特定的架构或模式**时，模块化可能会成为一个问题。当你和**的分布式团队**一起参与**的大型项目**时，这种问题就会出现。嗯，我在 **[Nest](https://nestjs.com/)** 中找到了解决方案，它提供了开箱即用的带有节点和类型脚本的服务器端架构。

## 窝哲学与好处

这是你可以在[nestjs.com](https://nestjs.com/):[![](img/052a46d986202c73c3f18ebcccb321ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aC9bXGrP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://samueleresca.net/wp-content/uploads/2018/02/28507035-160x160.png)上找到的定义

> 一个渐进式 Node.js 框架，用于构建高效且可伸缩的服务器端应用程序。让我们一起向全世界展示 Node.js 潜力吧！

Nest 是一个用于构建高效、可伸缩的 Node.js 服务器端应用程序的框架。它使用**类型脚本**，结合了 **OOP** 、**功能编程**和**反应编程**的元素。Nest 站在 express 之上，它实现了 MVC 模式。恕我直言，Nest 的**优势**在于它与其他语言的许多框架相似。ASP.NET、Django 和 Spring 开发者会发现 Typescript 的强类型方法和 Nest 的架构非常熟悉。与其他 MVC 框架一样，高度可测试性、可伸缩性、松耦合等特性是 Nest 的核心部分。

## **核心基本面**

Nest 使用**装饰器**来定义我们代码中的可识别组件。这种方法也被称为**元编程**，作为一名 ASP.NET 开发者，我发现它与 ASP.NET 已经实现的概念非常相似(例如:动作过滤器)。让我们来概述一下 Nest 的这些核心部分:

*   `@Controller([prefix])`:表示控制器。控制器层负责处理传入的**请求**，并向客户端返回**响应**；
*   任何事物都是一个组成部分。存储库、服务、助手必须作为组件受到威胁。组件可以通过构造函数注入到其他组件中；
*   它只是将一组组件组合在一起。此外，它们还负责所包含的组件、助手和控制器的依赖注入(它们可以与 C#类库相比；
*   中间件位于我们收到的请求和服务器之间。它通过管道流工作，它们可以用于身份验证、日志记录、重定向；

## 动手嵌套框架

我们来看一个 Nest.js 架构的具体例子。这个例子是理解 Nest.js 架构背后的基本概念的一种方式。您可以通过以下链接找到知识库:[https://github.com/samueleresca/Blog.NestGettingStarted](https://github.com/samueleresca/Blog.NestGettingStarted)。
此外，我们将在下一章中使用它作为**比较术语**，以便了解与其他常规框架的相似性。它只是公开了一些 rest APIs 来在 Sql server 数据库上存储信息。让我们来概述一下项目的关键部分...`Infrastructure/DependencyInstaller.ts`包含通过依赖注入注入的所有组件的定义: