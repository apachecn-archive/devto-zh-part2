# 第 003 集 MVC 的第一步-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-003---first-steps-with-mvc---aspnet-core-from-0-to-overkill-3jl8>

在这个帖子/视频中，我们最终从一些更严肃但简单的代码开始(不仅仅是像上一集一样的`dotnet new`)，从 ASP.NET 核心 MVC 开始，看一看构建控制器和视图。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/zDMPwuUbOwc](https://www.youtube.com/embed/zDMPwuUbOwc)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。

## [T1】简介](#intro)

在上一期中，我们为负责组管理的第一个组件准备了存储库，并初始化了第一个应用程序。现在，我们将从离开的地方开始，向应用程序添加 MVC 并添加一些组读/写逻辑。

## 将 MVC 添加到组合中

让 MVC 在应用程序中工作只需要几行代码，都在`Startup`类中。

首先我们将`services.AddMvc()`添加到`ConfigureServices`方法中。这将在依赖注入容器中注册 ASP.NET 核心 MVC 所需的服务。在下一篇文章中，我们将把我们自己的服务添加到容器中，但是现在，就这样。

注册了所需的服务后，我们只需要将 MVC 添加到请求处理管道中。为此，我们将`app.UseMvc()`添加到`Configure`方法中。我删除了`app.Run(...)`方法调用，但是只要是在 MVC 注册之后，就可以把它留在那里，如果不是这样，它将处理所有的请求，没有一个会到达我们的 MVC 开发。如果我们在 MVC 注册之后离开它，任何不被 MVC 处理的东西都将退回到`Hello World!`中间件。

就这样，现在我们可以开始使用控制器了。

## 创建第一个控制器

按照默认的项目组织，我们在应用程序的根目录下创建一个`Controllers`文件夹。

尽管这不是强制性的(许多人反对这样做)，因为 ASP.NET 核心 MVC 能够发现控制器，而不管它们在项目中的位置如何，只要它们满足以下要求之一:

*   类继承自`Controller`
*   类继承自`ControllerBase`
*   类名以`Controller`结尾
*   类是用`[Controller]`属性修饰的
*   类是用`[ApiController]`属性修饰的

> 这些是我所知道的 ASP.NET 核心能够发现控制器的方法。也许还有更多，如果你知道的话，给我留言补充一下🙂。此外，这个“魔术”适用于 web 应用程序项目中的控制器，对于其他项目中的控制器，它需要显式地完成，但这超出了我想在这篇介绍性帖子中涵盖的范围。

在新创建的`Controllers`文件夹中，我们添加了一个新类`GroupsController`。就像我提到的，仅仅因为名字以`Controller`结尾，MVC 就会找到它并假设它是一个控制器，其他什么都不需要。然而，由于我想利用一些已经存在的功能，我们将使这个新类继承自`Controller`类，因为它为构建我们的请求处理逻辑提供了几个有用的属性和方法。

在`GroupsController`类之上，我们添加了属性`[Route("groups")]`，它指示控制器可用的路线，在本例中是(在开发中)`http://localhost:5000/groups`。

现在，对于我们的第一个动作，我添加了一个返回`IActionResult`的`Index`方法。当我们定义 MVC 动作时，这是通常的返回类型，因为它允许通过调用基本的`Controller`类方法返回不同的响应和状态代码，如`NotFound`(返回 404)、`Content`(返回 200 加上提供的内容)或`View`(呈现按照约定发现的视图或作为参数传递的视图)。

只是为了看看实际行动(双关语)😛)，我们将按如下方式实现: