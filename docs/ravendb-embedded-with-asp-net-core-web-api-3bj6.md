# 嵌入 ASP NET 核心 web API 的 RavenDB

> 原文：<https://dev.to/rkosta/ravendb-embedded-with-asp-net-core-web-api-3bj6>

这篇文章描述了如何使用 Visual Studio 2017 设置一个简单的 ASP NET 核心 web API，以使用嵌入式 RavenDB。这是一个循序渐进的教程。

a)你需要用 Visual Studio 2017

创建一个新的 ASP.NET 核心 Web 应用 b)选择。网芯；ASP.NET 核心 2.1；API

c)右键新建项目- >管理 NuGet 包

d)你要添加一个新的包源。设置。新的包源。

姓名:[my get](https://www.myget.org/F/ravendb/api/v3/index.json)
T3】来源:【https://www.myget.org/F/ravendb/api/v3/index.json】T4

e)选择新源。添加:

RavenDB。嵌入式-4 . 1 . 1-nightly-2018 09 04-0430

RavenDB。客户-4 . 1 . 1-夜间-20180904-0430

f)创建一个 DocumentStoreHolder 类

```
internal class DocumentStoreHolder
{ 
    private static Lazy<IDocumentStore> store = new Lazy<IDocumentStore>(CreateStore); 

    public static IDocumentStore Store => store.Value; 

    private static IDocumentStore CreateStore() 
    { 
        var serverOptions = new ServerOptions() 
        { 
            ServerUrl = "http://127.0.0.1:60956/", 
        }; 

        EmbeddedServer.Instance.StartServer(serverOptions); 

        return EmbeddedServer.Instance.GetDocumentStore("MyRavenDBStore"); 
    }
} 
```

g)如此而已 [![🙂](../Images/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.org/images/core/emoji/11/72x72/1f642.png)

嵌入 ASP NET 核心 web API 的 RavenDB 的帖子最先出现在 T2 的博客 IT T3 上。