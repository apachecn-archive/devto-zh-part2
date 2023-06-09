# 第 004 集-程序和启动类-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-004---the-program-and-startup-classes---aspnet-core-from-0-to-overkill-4mh1>

在这个帖子/视频中，我们回过头来看看 ASP.NET 核心应用的一些重要基础设施部分——`Program`和`Startup`类。我们的参考项目的真正进展将在一些剧集中暂停，因为我们专注于核心概念，这些概念有助于理解我们未来将做什么。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/kc4PzBHK2fA](https://www.youtube.com/embed/kc4PzBHK2fA)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。

## [T1】简介](#intro)

在上一集中，我们使用 MVC 启动了我们的组管理应用程序。然而，今天我们将后退一步来看看 ASP.NET 核心中的一些核心概念，这样我们可以更好地理解未来会发生什么，而不会同时被太多的概念淹没。

## 程序类

### 只是一个控制台应用程序

ASP.NET 核心应用程序基本上是一个托管 web 服务器(Kestrel)的控制台应用程序。了解了这一点，毫不奇怪会发现一个具有传统控制台应用程序中常见的经典`Main`方法的`Program`类。

这意味着在运行服务器之前，我们可以像在控制台应用程序上一样做几乎任何事情，主要是我们不想在 ASP.NET 核心应用程序本身的上下文中做的事情。例如，我在这里看到的做法是检查数据库是否是最新的，并在启动应用程序之前应用任何所需的迁移，尽管有人可能会说:

1.  不应该在应用程序启动时自动进行这种数据库更改(至少在生产环境中)
2.  即使我们真的想对数据库进行修改，我们也可以在`Startup`类中完成(或者准备 web 主机的另一个步骤)。

### 构建 web 主机

为了让我们的应用程序监听 HTTP 请求，我们必须创建一个`WebHost`。在 ASP.NET 核心 2.0 之前，我们需要做更多的配置来让它运行，但是在那个版本中，添加了一些帮助方法，我们只需要一行代码就可以启动`WebHost`:`WebHost.CreateDefaultBuilder(args).UseStartup<Startup>();`(通过命令行启动应用程序时传入的`args`参数)。

当然，这个助手方法只是抽象出每次我们想要创建一个新的应用程序时需要做的事情。如果我们对默认设置不满意，我们可以绕过这个助手，自己来做。作为参考，我们可以去看看 GitHub 上 helper 方法的源代码- [这里是 ASP.NET Core 2.1 上版本的链接](https://github.com/aspnet/MetaPackages/blob/96be626c87c3ca325b18aa6653602f5e7087497f/src/Microsoft.AspNetCore/WebHost.cs#L150)。

查看`CreateDefaultBuilder`代码，我们看到一些使用过 ASP.NET Core<2.0 的人会认识到的东西，比如配置 Kestrel 的使用，定义应用程序的根内容目录，设置配置源，设置日志记录和其他一些事情。将它抽象出来是很好的，因为很多时候它就足够了，但是知道它在那里是很重要的，我们可以覆盖它来满足我们的需要。

除了`CreateDefaultBuilder`之外，我们还看到了对`UseStartup<Startup>()`的调用。我们一会儿将仔细看看`Startup`类，但是这个方法调用基本上告诉`WebHostBuilder`应该用于初始化目的的类。这样做是可选的，因为我们可以不使用`Startup`类，而是直接在`WebHostBuilder`上调用`ConfigureServices`和`Configure`。

为了让我们的应用程序运行起来，我们调用`Build`从`WebHostBuilder`中获取`WebHost`的实例，然后在其上启动`Run`。

## 启动类

正如我前面提到的，`Startup`类包含了我们的 ASP.NET 核心应用程序的一些初始化代码，即以`ConfigureServices`和`Configure`方法的形式。

这些方法的名字并不重要，但为了清楚起见，`ConfigureServices`是我们配置依赖注入的地方——正如我们在上一集看到的，`services.AddMvc()`将 MVC 所需的服务添加到 DI 容器中——而`Configure`是我们配置请求处理管道的地方——正如我们看到的，`app.UseMvc()`将 MVC 的请求处理中间件添加到应用程序的管道中。

现在让我们用这些方法再玩一会儿，更好地了解它们的用途，为下一集更深入地学习做准备。

### 配置方法

正如我们所说的，`Configure`方法是我们配置请求处理管道的地方。假设我们正在构建一个 MVC 应用程序，我们添加了`app.UseMvc()`来注册 MVC 的中间件来处理请求。MVC 中间件应该是最后注册的，因为在大多数情况下，我们希望它是我们请求的最终处理程序，但是我们可以在管道的早期添加一些中间件来丰富请求/响应，甚至处理我们不希望 MVC 处理的事情。

#### 创建简单的中间件

让我们从创建一个非常简单的中间件开始，它为每个响应添加一个头。为了保持简单，我们将使用`app.Use(...)`方法(`IApplicationBuilder`的扩展方法)并传递给它一个表示中间件的 lambda 函数。有更多的方法来创建中间件，我们将在下一集看到这些。

正如我们所说的，中间件的基本思想是它们像链一样工作——一个中间件被调用，它可以做一些事情，然后将请求传递给下一个中间件，最后在下一个中间件完成时做其他事情。可能会出现下一个中间件没有被调用的情况，例如在授权中间件中，如果用户没有通过身份验证，它可以立即停止请求，自己返回一个响应。鉴于此，中间件的基础如下: