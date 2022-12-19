# Azure vs GCP 第 2 部分:比较 Web 应用和应用引擎服务

> 原文：<https://dev.to/kenakamu/azure-vs-gcp-part-2-compare-web-apps-and-app-engine-service-1kph>

在[上一篇文章](https://dev.to/kenakamu/azure-vs-gcp-part-1-aspnet-developer-experience-22i)中，我以 C#开发者的身份比较了 Azure Web Apps 和 GCP App Engine 的部署体验，没有想太多。

然后我意识到我应该从不同方面比较它们，比如 linux 平台或容器部署。但是在继续部署之前，我稍微后退一步，从服务角度对它们进行比较。

# Azure 网络应用:Windows 和 Linux

Azure Web Apps 提供了两种环境，Windows 和 Linux。它们非常相似，但是对于 Linux 环境，您可以构建自己的 docker 映像并进行部署。

# GCP App 引擎:标准灵活

GCP 也有两种类型的应用引擎，标准和灵活(又名应用引擎灵活)我认为他们是完全不同的服务，因为有这么多的差异。

每个服务都有详细的文档，这个表很好地描述了它们的区别。

#### 比较高级特征

| 特征 | 灵活的环境 | 标准环境 |
| --- | --- | --- |
| 实例启动时间 | 分钟 | 秒 |
| 最大请求超时 | 60 分钟 | 60 秒 |
| 后台线程 | 是 | 是的，有限制 |
| 后台进程 | 是 | 不 |
| SSH 调试 | 是 | 不 |
| 缩放比例 | 手动、自动 | 手动、基本、自动 |
| 正在写入本地磁盘 | 是，短暂的(每次虚拟机启动时初始化磁盘) | 不 |
| 修改运行时 | 是(通过 Dockerfile) | 不 |
| 自动安装安全补丁 | 是(不包括容器映像运行时) | 是 |
| 网络存取 | 是 | 仅通过 App Engine 服务(包括出站套接字)，并且仅用于启用计费的 Python、Go 和 PHP 应用程序。 |
| 支持安装第三方二进制文件 | 是 | 不 |
| 位置 | 北美、亚太地区或欧洲 | 北美、亚太地区或欧洲 |
| 定价 | 基于 vCPU、内存和持久磁盘的使用情况 | 基于实例小时数 |

虽然它们非常不同，但我把它们视为一个服务，并与 Azure Web Apps 进行比较。

# 有什么共同之处

他们在许多方面是相似的。

*   支持多种语言和运行时:。NET Core，Java，Ruby，Node.js，PHP，Python，Go 等。
*   IDE 集成:Azure Web Apps for Windows 和 App Engine Flexible 提供 Visual Studio 集成。
*   SSH/CMD:您可以通过 SSH 或 CMD 访问下划线 VM。
*   工具:除了 IDE 集成之外，两个平台都提供了易于使用的 CLI 工具。CLI 可以在您的客户端和浏览器中运行。
*   性能和维护:自动扩展、负载平衡、安全性、灵活的 CPU/内存配置、自动操作系统管理。
*   平台级支持:可以利用 IAM、监控、网络等云平台。

# 有什么不同

当然也有一些不同之处。

### 语言

是的，它们是相似的，但也是不同的。

*   满了。NET:由于它需要 Windows 操作系统，只有 Azure 支持它。
*   Go:在 Azure 上，需要使用 docker image 来支持 Linux 版本的 Go，而 GCP 是原生支持的。
*   次要版本:当然有次要版本差异。

### DevOps

当然，我们可以为任何平台做 CI/CD，但 Azure 提供 DevOps 功能作为 Web 应用的一部分。我还可以直接推送网页应用程序。

# 我还不知道什么

我对 GCP 不熟悉，所以我对管理类型的功能还不太了解。

*   监视
*   扩展和负载平衡设置
*   安全选项

我还不知道开发商相关的事情，如

*   实时调试体验
*   与数据存储的连接
*   认证选项

我将在后面介绍它们，但是在下一篇文章中，我将从 Linux 平台的角度比较 Web 应用程序和灵活的应用程序引擎！

# 引用

[Azure Web Apps 概述](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-overview)
[GCP App Engine 灵活概述](https://cloud.google.com/appengine/docs/flexible/)