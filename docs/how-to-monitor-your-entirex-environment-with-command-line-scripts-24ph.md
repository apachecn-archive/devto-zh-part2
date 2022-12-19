# 如何使用命令行脚本监控整个环境

> 原文：<https://dev.to/techcommunity/how-to-monitor-your-entirex-environment-with-command-line-scripts-24ph>

# webMethods EntireX

EntireX 用于分布式和混合环境，用于 COBOL 或自然集成。EntireX 监控脚本为您提供了一种监控 EntireX 环境的简单方法。看看从命令行监控 EntireX 代理以及 ACI 或 RPC 服务器是多么容易。

Adabas & Natural 产品管理总监 Juergen Lind 和 Software AG 高级首席软件工程师 Peter Hofer

| 2018 年第二期 | [![](../Images/a80ab71273994c8d589f4e03e98b42bb.png) 下载 PDFT4】](http://techcommunity.softwareag.com/ecosystem/download/techniques/2018-issue2/SAG_webMethods_EntireX_TECHniques_Apr18_WEB.pdf) |
| --- | --- |

## 简介

想象一下这个场景:您有一个完整的生产环境，其中包含您想要监控的代理和 RPC 或 ACI 服务器。您还希望在环境的某个组件停止运行并且不再可用时收到通知。

为此，您可以使用 EntireX 提供的监控脚本。它们允许您定义您的环境，监视它并定义在出现错误时的操作。虽然脚本将在 Microsoft Windows 上安装和运行，但是您可以监视在任何其他操作系统上运行的 EntireX 环境。

在 Windows 上安装完 EntireX 之后，你可以在 *<下找到监控脚本的快捷方式，你的安装名称> > >管理*如图 1 所示。

[![](../Images/aadc697181562aab0937d6a4e5f0327a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7IefFSC8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/EntireX_1.jpg/9aa522b3-78bf-4c81-9bca-d7ed598ab6be%3Ft%3D1523445358663) 

*图 Windows 开始菜单中的 EntireX 监控脚本*

使用监视脚本菜单中提供的功能来定义和监视您的环境。截图中显示了函数 7 和 8(参见图 2)。

[![](../Images/ed7180d59660cdc45925339739566229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aBlA0yPq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/10157/9581666/EntireX_2.jpg/fb92fe0c-7487-4b7c-ba91-f291d6d1a758%3Ft%3D1523445361866) 

*图 EntireX 监控脚本菜单*

## 定义你的环境

选择功能 7 *定义您的环境*来定义您的环境。这将打开一个带有环境定义示例的编辑器。根据你的需要调整这个定义。

这里你看到一个基本环境的例子:

环境 MyProductionServers

error exit handle _ error . bat

BROKER myProductionBroker localhost:1971

@ rem RPC 服务定义的示例

RPC Service myRPCServer RPC SRV 1 CALLNAT

@ rem ACI 服务定义的示例

Service myACIServer aclassasserver as Service

环境由关键字 *ENVIRONMENT* 定义，并带有您自定义的有意义的环境名称。它需要定义:

*   用户编写的用于处理停机的出口
*   完整的经纪人
*   ACI 或 RPC 服务

请注意，EntireX 代理不一定要在本地 PC 上运行；相反，它可以在网络中的任何地方运行(只要可以从运行监控脚本的 PC 上访问 EntireX 代理)。

使用关键字 *ERROREXIT* 定义您自己的错误例程(即 bat 文件),以便在断电时调用。bat 文件必须在*路径*中，以便监控脚本可以调用它。在发生故障的情况下，将调用错误出口，其中包含有关导致错误的组件(代理或服务器)的信息。见提供的文件 *handle_error.bat* 举例。根据您的需要调整错误出口(例如在断电的情况下发送通知)。

使用关键字 *BROKER* ，后跟您的代理逻辑名称和代理 ID，以定义要监控的代理。如果您的代理安全运行，那么如何指定用户 ID 和密码，请参见提供的示例环境。

使用关键字 *RPCSERVICE* (对于 RPC 服务器)或 *SERVICE* (对于 ACI 服务)，后跟您的逻辑名称以及类、服务器和服务，以定义要监控的服务器。您可以定义多个服务器。

示例:

RPC service myCICSRPCServer RPC CICS SRV CALLNAT

@ rem my CICS RPC Server

RPC service myBATCHRPCServer RPC Batch SRV CALLNAT

@ rem my Batch RPC Server

在这种情况下，监控脚本将首先检查 CICS RPC 服务器是否可以被调用。其次，它将检查批处理 RPC 服务器的可用性。

环境的定义存储在*My Documents/Software AG/EntireX*下

## 监控你的环境

选择功能 8 *监控您的环境*开始监控您在上一步中定义的环境。在执行过程中，环境的定义将被传递。关键字 *BROKER* 检查定义的 BROKER 是否可以被访问。

关键字 *SERVICE* 检查定义的(ACI)服务是否在代理中注册。

关键字 *RPCSERVICE* 定义了一个 RPC 服务器。在执行过程中，将使用 RPC-PING 对该服务进行“PING”。这将检查定义的服务是否可以被调用。

如果出现中断或错误情况，将调用您自定义的、用户编写的错误出口。

在指定的时间(默认值为 60 秒)后，将重复对环境的整个检查。

您可能会问自己，“这些检查最终是如何执行的？”实际上，所有检查都会调用命令行实用程序 *etbinfo* 。

## 处理停机

如果对环境*(代理、服务或*、T2【RPC SERVICE】、T4】 ) 的组件的检查失败，则调用指定的 *ERROREXIT* ，并提供相关信息

*   对象*(经纪人或服务)*
*   由 *ETBINFO* 返回的错误号和错误文本
*   环境名称
*   代理名称和代理 ID
*   在服务失败的情况下，名称、类别、服务器和服务

在中断的情况下，您可以使用此信息发送关于所检查的环境(使用您定义的环境名)和不可用的组件(代理或服务)的通知。或者您可以尝试自动重启丢失的组件。

要了解更多，请阅读 [webMethods EntireX 文档](http://techcommunity.softwareag.com/ecosystem/documentation/webmethods/index.htm)中的*用命令行脚本监控 EntireX*。

## 总结

使用 EntireX 监控脚本，您可以轻松监控您的 EntireX 景观。您可以将要监控的 EntireX 组件自定义为一个环境，可以监控 EntireX 代理以及 ACI 或 RPC 服务器。

在断电的情况下，将调用用户定义的错误出口，其中包含您可以根据需要调整的所有信息。例如，您可能想要发送通知或重新启动不可用的组件。