# 将您现有的 ASP.NET 核心 1.1 应用升级到 ASP.NET 核心 2

> 原文：<https://dev.to/arkasoftwares/upgrade-your-existing-aspnet-core-11-applications-to-aspnet-core-2-289e>

微软最近发布了他们开源的 ASP.NET 核心 2 以及跨平台框架和库。这是一个相当令人兴奋的版本，这是迈向框架稳定的一步，使开发者和企业开始考虑使用 ASP.NET 2.0。这就是为什么它为. net 开发服务开辟了新的途径。让我们来看看如何将现有的 ASP.NET 核心 1.1 应用升级到 ASP.NET 核心 2。

[![](../Images/e6256b8ab9c3c713c512a8a8a39696f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_PLvS8qn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.allaboutweb.biz/wp-content/uploads/2018/04/What-is-new-in-ASP.NET-Core-2.0-1140x500.jpg)

安装 ASP.NET 核心 2.0
你要做的第一步是安装新的 2.0 ASP.NET，如果你使用 Visual Studio 作为你的 IDE 选择，那么你必须安装最新版本的 Visual Studio 15.3.x，以便与新的核心 2.0 一起工作。这些步骤非常简单，并且有据可查。

**MVC 项目的升级**
当你已经在 Visual Studio 中加载了 web 解决方案，那么你的第一个任务就是升级 web 项目，然后让它运行起来。因此，卸载测试项目，这样您就不会因为错误而分心。

**项目文件以及依赖关系的升级**
现在，接下来要做的是将您的项目升级到目标 ASP.NET 核心 2.0，并升级相关的依赖关系以请求。NET Core 2.0 包。单击项目，然后选择直接编辑名为 cs proj 的文件。与。NET 核心项目，你可以很容易地做到这一点，没有任何要求卸载第一。此外。NET Core 项目有一个名为 target Framework 的节点，需要设置为 net core app 1.0。要升级目标(最近的目标框架名字或核心 2.0 的 TFM)，只需将其更改为 norecoreapp2.0

该项目还被包括在被设置为特定 1.0.4 的运行时框架版本属性中，该属性被移除以确保该项目可以使用最近可用的运行时。迁移指南也确保了这一点，并指定包目标回退节点以及变量也应该重命名为资产目标回退，这是需要完成的。这是 asp dot net 开发的一个重要方面。
**身份和认证的变化**
在 ASP.NET 核心 2.0 中，一些最重要的变化发生在身份命名空间中。微软在身份模型和认证方面做了一些调整。这些变化之前需要进行一些修复以及代码重组，以符合最新的新模型。Microsoft 已经整理了一个特定的迁移文档，如果您必须迁移身份代码，该文档值得查看。

在 ASP.NET 核心 2.0 的情况下，其中一个主要变化也与身份有关，它是第三方登录提供者，现在只要有认证服务的注册就进行配置，它们不再需要作为特定的单独中间件组件添加。

### 相关- [知道为什么 ASP DOT NET 会继续统治商业技术](https://www.arkasoftwares.com/blog/know-why-asp-dot-net-will-continue-to-rule-business-technology/)

要考虑这一特定更改，请更新 Configure Services 方法，以便在 IService 集合上使用最新的 Add Authentication extension 方法。它还包括特别返回的身份验证构建器上的各种扩展方法，可用于添加和配置给定的附加身份验证提供者。只有在应用程序配置包含每个提供者所需的客户端 ID 或应用程序的情况下，您才需要有条件地注册提供者。它对 Add 身份验证方法进行了不同的可选调用。

**重要迁移变更**
为了充分利用 ASP.NET 核心 2.0 的约定，可以而且应该对现有的 ASP.NET 核心 1.0 项目进行一些结构变更。第一件事是更新 program.cs 以使用最新的 Create Default Builder 功能。这种特殊的方法是专门为简化 ASP.NET 核心 Web 主机的设置而设计的，它通过定义一些常见的缺省值来实现，而这些缺省值是您以前必须在启动类中手动设置的。例如，它增加了 Kestrel 以及 IIS 集成。中的 IWeb 主机。NET Core 2.0 还设置日志和配置，并在应用程序生命周期的早期将它们注册到 DI。

### 结论

对于最重要的部分，微软提供的迁移文档非常好，涵盖了很多内容。在少数情况下，您可以找到一些额外的事情需要解决。在很大程度上，突出的问题将围绕着测试以及 EF 的变化，可能需要更长的时间来隔离和修复。能够将项目向前推进，然后在发布后很快将其升级到 2.0 版是非常棒的。这就是为什么[ASP . net 开发服务](https://www.arkasoftwares.com/asp-dot-net-application-development)依赖 ASP.NET 核心 2.0。这将有助于开发人员查看并开发真实世界的 ASP.NET 酷睿 2.0 应用和解决方案。

## 标签:

点网络开发服务，asp 点网络开发，asp 网络开发服务