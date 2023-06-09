# 使用 LazyCache 在 ASP.NET 核心中进行缓存

> 原文：<https://dev.to/lukencode/caching-in-aspnet-core-with-lazycache-1cp8>

缓存是提高应用程序性能的一个很好的方法——要么支持高负载场景，要么掩盖一些糟糕的代码，让生活变得可以忍受。我一直喜欢一个简单的“GetOrCreate”风格的缓存 API，所以当我发现由[阿利斯泰尔·克拉布特里](https://alastaircrabtree.com/)开发的开源软件 [LazyCache](https://github.com/alastairtree/LazyCache) 时，我就迫不及待地想尝试一下。

LazyCache 将自己描述为“一个基于内存缓存服务的易于使用的线程安全泛型，带有一个简单的面向 C#的开发人员友好的 API”，我对此非常赞同。

ASP.NET 核心版本目前处于测试阶段(尽管看起来很稳定)，可以通过 nuget 安装

```
Install-Package LazyCache.AspNetCore -Version 2.0.0-beta03 
```

Enter fullscreen mode Exit fullscreen mode

安装后，在启动时配置服务时可以很容易地使用它:

```
services.AddLazyCache(); 
```

Enter fullscreen mode Exit fullscreen mode

这将在整个应用程序中注入 IAppCache。

下面是一个在主页 my[Australia n tech jobs board-austech jobs](https://austechjobs.com.au)上使用 LazyCache 的简单例子:

IAppCache 是注入到我的类中的惰性缓存服务。它提供了一个 *GetOrAddAsync* 方法，该方法接受:

*   一个缓存键，在我的例子中是字符串“HomeModel”。
*   以 Func <icacheentry task="">addItemFactory 函数的形式检索要缓存的数据的工厂。我的示例中的代码在进行数据库调用后返回一个模型(这是我想要缓存的部分)。</icacheentry>
*   缓存长度选项。我使用 12 小时的简单滑动时间跨度。

我发现这种缓存设置的简单性非常令人满意。它的实现使用 Lazy <t>来确保工厂方法只执行一次。默认情况下，LazyCache 将使用 Microsoft . extensions . caching . memory 中的 IMemoryCache。这种实现将只在运行它的机器上缓存数据。如果我在运行一个更密集的服务，我会扩展 [LazyCache](https://github.com/alastairtree/LazyCache/wiki/2.0-Extending-LazyCache-with-Redis,-Cassandra-etc) 并实现类似 redis 的分布式缓存。好消息是，因为我在整个应用程序中使用 IAppCache，所以我可以相对容易地更改底层缓存提供者。</t>