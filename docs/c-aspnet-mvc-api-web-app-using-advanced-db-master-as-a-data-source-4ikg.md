# 使用高级 DB Master 作为数据源的 c # ASP.Net MVC API Web 应用程序

> 原文：<https://dev.to/cfleischhacker/c-aspnet-mvc-api-web-app-using-advanced-db-master-as-a-data-source-4ikg>

一直想知道如何在后端使用 ADBM(高级 DB Master)数据源构建一个 C#、ASP.Net、MVC、API 的 web app？不客气

[GitHub 上的演示源](https://github.com/cjfleischhacker/ADBM.API)。

从[http://maconsys.com](http://maconsys.com)安装高级 DB Master (ADBM ),然后打开示例数据库 ADBMSamp，并退出 ADBM。

ADBM 在你的操作系统中添加了一个名为 adbmsamp 的系统 DSN ODBC 数据源。

[![SystemDSN.PNG](../Images/11187af51bba40788c7c944ea40464d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eFQIaC_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisfleischhacker.files.wordpress.com/2018/03/systemdsn.png%3Fw%3D646)

在 Visual Studio 中，创建 C # ASP.Net 框架或核心 Web 应用程序。

[![VisualStudioProject.PNG](../Images/47c615b1413f143201c7058e1599b429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ByuumZm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisfleischhacker.files.wordpress.com/2018/03/visualstudioproject.png%3Fw%3D646)

并选择 Web API，保持选中 MVC 和 Web API 框。

[![WebAPI.PNG](../Images/d1179f5f3cde3bea03124623a83068f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W4KpRuzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisfleischhacker.files.wordpress.com/2018/03/webapi.png%3Fw%3D646)

或者，您可以将数据源添加为数据库连接，并根据自己的喜好使用它，但这不是必需的。

[![DatabaseConnection.PNG](../Images/3a94ef846d2366d4e06a66ca5aedbe33.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qRV1PesK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://chrisfleischhacker.files.wordpress.com/2018/03/databaseconnection.png%3Fw%3D646)

根据我们的需要，我们只需要 web.config 中的一个连接字符串:

 <connectionstrings><add name = " csADBMSamp " connectionString = " DSN = adbmsamp "/>

</connection strings></connectionstrings>