# 第 005 集-依赖注入-ASP.NET 核心:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-005---dependency-injection---aspnet-core-from-0-to-overkill-50e7>

在这篇文章/视频中，我们继续探讨 ASP.NET 核心的基本概念，为构建应用做准备。这一次，依赖注入被嵌入到了 ASP.NET 核心的核心部分(看我在那里做了什么？😆).

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/Dj8CKHokDsQ](https://www.youtube.com/embed/Dj8CKHokDsQ)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。

## [T1】简介](#intro)

在上一期中，我们开始研究开发 ASP.NET 核心应用程序时的一些重要核心概念，即`Program`和`Startup`类，并快速研究了依赖注入和中间件。在这一集里，我们对依赖注入做了进一步的探讨。

## 从控制器中提取逻辑

### 为什么

通常我们不希望我们的控制器有太多的逻辑，尤其是业务逻辑。理想情况下，控制器专注于 MVC/HTTP 特定的逻辑——接收客户端输入，将它们传递给负责业务逻辑的某个部门，返回结果(反馈视图或其他形式，如 JSON 有效负载),根据业务逻辑的输出，可能包括不同的 HTTP 状态代码。

当然，这不是强制性的，我也看到过一些文章和讨论，比如说，如果我们正在开发非常小的微服务，我们可能会绕过这样的代码分割，如果需要调整，我们可以重写服务。无论如何，我将采用更经典的拆分方法，只是想说明这不是一成不变的(因为没有什么是真的，总是有多种方法来做事情)。

将业务逻辑从 web 框架中分离出来的一些好处是，能够在没有 web 框架特性所增加的复杂性的情况下对其进行测试，以及允许将相同的逻辑组件与不同的“网关”(替代的 web 框架、桌面应用程序等)一起使用。

### 新建类库

为了从控制器中提取业务逻辑，我们将沿用老式的 3 层架构，其中我们的 MVC 应用程序将是表示层，提取的逻辑将构成业务层。还没有数据层，我们将在未来得到它。

n 层体系结构可能不是现在最热门的，但对于我们现在正在做的事情来说，它符合我们的需求，而且足够简单。

从创建两个类库`CodingMilitia.PlayBall.GroupManagement.Business`和`CodingMilitia.PlayBall.GroupManagement.Business.Impl`开始。第一个库将包含业务逻辑的客户需要知道的契约/API，而后者将包含所述逻辑的实现。

在合同库中，我们需要模型(在这种情况下只需要`Group`类)和`IGroupService`接口。

```
public class Group
{
    public long Id { get; set; }
    public string Name { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public interface IGroupsService
{
    IReadOnlyCollection<Group> GetAll();

    Group GetById(long id);

    Group Update(Group group);

    Group Add(Group group);
} 
```

Enter fullscreen mode Exit fullscreen mode

转到服务实现库，我们创建了一个实现了`IGroupsService`接口的`InMemoryGroupsService`类——现在没有太多的业务逻辑，大部分是 CRUD，但是让我们假设有😛

```
public class InMemoryGroupsService : IGroupsService
{
    private readonly List<Group> _groups = new List<Group>();
    private long _currentId = 0;

    public IReadOnlyCollection<Group> GetAll()
    {
        return _groups.AsReadOnly();
    }

    public Group GetById(long id)
    {
        return _groups.SingleOrDefault(g => g.Id == id);
    }

    public Group Update(Group group)
    {
        var toUpdate = _groups.SingleOrDefault(g => g.Id == group.Id);

        if (toUpdate == null)
        {
            return null;
        }

        toUpdate.Name = group.Name;
        return toUpdate;
    }

    public Group Add(Group group)
    {
        group.Id = ++_currentId;
        _groups.Add(group);
        return group;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

顾名思义，数据存储在内存中(它甚至不是线程安全的)，所以说它离生产就绪还很远是一种保守的说法。这与我们在控制器中使用的逻辑完全相同，只是被纳入了一个独立的类中。

回到 web 应用程序项目，我们添加对新创建的库的引用，并修改`GroupsController`以使用`IGroupsService`而不是在其中实现逻辑。

```
[Route("groups")]
public class GroupsController : Controller
{
    private readonly IGroupsService _groupsService;

    public GroupsController(IGroupsService groupsService)
    {
        _groupsService = groupsService;
    }

    [HttpGet]
    [Route("")]
    public IActionResult Index()
    {
        return View(_groupsService.GetAll().ToViewModel());
    }

    [HttpGet]
    [Route("{id}")]
    public IActionResult Details(long id)
    {
        var group = _groupsService.GetById(id);

        if (group == null)
        {
            return NotFound();
        }

        return View(group.ToViewModel());
    }

    [HttpPost]
    [Route("{id}")]
    [ValidateAntiForgeryToken]
    public IActionResult Edit(long id, GroupViewModel model)
    {
        var group = _groupsService.Update(model.ToServiceModel());

        if (group == null)
        {
            return NotFound();
        }

        return RedirectToAction("Index");
    }

    [HttpGet]
    [Route("create")]
    public IActionResult Create()
    {
        return View();
    }

    [HttpPost]
    [Route("")]
    [ValidateAntiForgeryToken]
    public IActionResult CreateReally(GroupViewModel model)
    {
        _groupsService.Add(model.ToServiceModel());

        return RedirectToAction("Index");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有几件事需要注意:

*   我们现在在构造函数中接收`IGroupsService`,所以我们可以在动作中使用它
*   `ToViewModel`和`ToServiceModel`扩展方法——由于表示层和业务层使用不同的模型集，我们需要在传递它们时对它们进行转换

```
public static class GroupMappings
{
    public static GroupViewModel ToViewModel(this Group model)
    {
        return model != null ? new GroupViewModel { Id = model.Id, Name = model.Name } : null;
    }

    public static Group ToServiceModel(this GroupViewModel model)
    {
        return model != null ? new Group { Id = model.Id, Name = model.Name } : null;
    }

    public static IReadOnlyCollection<GroupViewModel> ToViewModel(this IReadOnlyCollection<Group> models)
    {
        if (models.Count == 0)
        {
            return Array.Empty<GroupViewModel>();
        }

        var groups = new GroupViewModel[models.Count];
        var i = 0;
        foreach (var model in models)
        {
            groups[i] = model.ToViewModel();
            ++i;
        }

        return new ReadOnlyCollection<GroupViewModel>(groups);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以直接在控制器中进行这种映射，但最终会被这些样板代码所污染。我们也可以使用 [AutoMapper](https://github.com/AutoMapper/AutoMapper) 来自动完成这项工作，但是它有时会隐藏我们代码中的问题(例如，当使用它时，我们不能确定是否存在对属性的引用)。总而言之，我喜欢我的一个同事提出的这个扩展方法的想法，因为它在控制器中提供了很好的可读性，所以我会采用它。

如果我们现在尝试运行它，我们会得到一个错误，因为我们现在期望在控制器中得到一个`IGroupsService`，但是我们还没有告诉框架如何得到它。为此，我们需要在依赖注入容器中注册服务实现。

## 使用内置容器

### 注册服务

在前一集，我们看到了如何在内置依赖注入容器中注册服务，这也没有什么不同。我们希望注册`InMemoryGroupsService`,就像我们之前看到的一样，我们希望它拥有 singleton 类型的生活方式，这样它保存在内存中的数据在应用程序的整个生命周期中都不会丢失。

为此，我们需要向`Startup`类添加一个新行:`services.AddSingleton<IGroupsService, InMemoryGroupsService>();`。

现在，我们可以运行应用程序，它的行为与以前一样，只是内部组织不同。

### 改善组织

现在，我们没有太多的担心，因为我们在`Startup`类的`ConfigureServices`方法中只有几行代码，但是当我们添加越来越多的代码时会怎么样呢？

保持`ConfigureServices`整洁的一个好方法是在`IServiceCollection`上创建扩展方法来注册一起运行的服务组。考虑到这一点，在新创建的`IoC`文件夹中，我们添加了一个新的类`ServiceCollectionExtensions`。

```
namespace Microsoft.Extensions.DependencyInjection
{
    public static class ServiceCollectionExtensions
    {
        public static IServiceCollection AddBusiness(this IServiceCollection services)
        {
            services.AddSingleton<IGroupsService, InMemoryGroupsService>();

            //more business services...

            return services;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的名称空间“trick ”,使用与`IServiceCollection`相同的名称空间。这通常是为了简化扩展方法的可发现性，因为它允许我们转到`ConfigureServices`方法，键入`services.`并立即获得智能感知中的方法，而无需添加另一个`using`。

现在在`ConfigureServices`中，我们可以用`services.AddBusiness();`替换之前添加的行。

## 使用第三方容器

ASP.NET 核心自带内置的依赖注入，这很好，但我们需要记住，开箱即用的容器是为了满足 ASP.NET 核心的内部需求而构建的，而不是基于它构建的应用程序的每一个需求。

对于更高级的场景，我们可以使用能够提供这些特性的第三方 DI 容器。

为了验证这种可能性，我们将把 [Autofac](https://github.com/autofac/Autofac) 添加到项目中，并对其进行一些小的改动。我们的目标不是深入研究 Autofac 本身，而是看看如何将它与 ASP.NET 核心一起使用，以及它所添加的额外功能的一个示例。

### 更换容器

首先要添加几个 NuGet 包- `Autofac`和`Autofac.Extensions.DependencyInjection`。

为了向 Autofac 注册依赖项，我们创建了一个继承自`Module`的新类`AutofacModule`(在`IoC`文件夹中),并覆盖了`Load`方法。

```
public class AutofacModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder
            .RegisterType<InMemoryGroupsService>()
            .As<IGroupsService>()
            .SingleInstance();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面看到的代码相当于我们在`AddBusiness`中的代码，但是适应了 Autofac 的 API。现在我们需要将 Autofac 配置为容器。

```
//...
public IServiceProvider ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    //if using default DI container, uncomment
    //services.AddBusiness();

    // Add Autofac
    var containerBuilder = new ContainerBuilder();
    containerBuilder.RegisterModule<AutofacModule>();
    containerBuilder.Populate(services);
    var container = containerBuilder.Build();
    return new AutofacServiceProvider(container);
}
//... 
```

Enter fullscreen mode Exit fullscreen mode

要注意的第一个变化是`ConfigureServices`方法不再是`void`，它现在返回一个`IServiceProvider`。这是必需的，因此 ASP.NET 核心使用返回的服务提供者，而不是从作为参数接收的`IServiceCollection`中构建自己的服务提供者。

方法末尾的 Autofac 配置代码是从微软的依赖注入文档中复制粘贴的😇

基本上，我们现在做的是:

*   创建 Autofac 容器生成器
*   用我们的依赖配置注册我们创建的模块
*   用出现在`IServiceCollection`中的注册填充 Autofac 的容器，由 ASP.NET 核心的组件放在那里
*   构建容器
*   返回一个新的服务提供者，使用 Autofac 容器实现

再次，重新运行应用程序，我们保持相同的功能。

### 使用其他容器特性

到目前为止，我们还没有使用 Autofac 的任何特定功能来证明这一变化，除非是性能问题(这种情况不会发生，至少在编写本文时，内置容器似乎更快，见[此处](https://github.com/danielpalme/IocPerformance))。

为了便于讨论，让我们使用 Autofac 提供的一个额外特性:decorators。

```
public class AutofacModule : Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder
            .RegisterType<InMemoryGroupsService>()
            .Named<IGroupsService>("groupsService")
            .SingleInstance();
        builder
            .RegisterDecorator<IGroupsService>(
            (context, service) => new GroupsServiceDecorator(service), 
            "groupsService");
    }

    private class GroupsServiceDecorator : IGroupsService
    {
        private readonly IGroupsService _inner;

        public GroupsServiceDecorator(IGroupsService inner)
        {
            _inner = inner;
        }

        public IReadOnlyCollection<Group> GetAll()
        {
            Console.WriteLine($"######### Helloooooo from {nameof(GetAll)} #########");
            return _inner.GetAll();
        }

        public Group GetById(long id)
        {
            Console.WriteLine($"######### Helloooooo from {nameof(GetById)} #########");
            return _inner.GetById(id);
        }

        public Group Update(Group group)
        {
            Console.WriteLine($"######### Helloooooo from {nameof(Update)} #########");
            return _inner.Update(group);
        }

        public Group Add(Group group)
        {
            Console.WriteLine($"######### Helloooooo from {nameof(Add)} #########");
            return _inner.Add(group);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

装饰器实现了`IGroupsService`接口，在每次方法调用时写入控制台，然后将真正的工作委托给它在构造函数中获得的另一个`IGroupsService`实现。

为了注册装饰器，我们将`InMemoryGroupsService`注册更改为命名注册，然后用 lambda 调用`RegisterDecorator`来构建装饰器，并将名称添加到实际实现的注册中。不能说我是这个 API 的最大粉丝，但这就是我们现在所拥有的。

现在，如果我们重新运行应用程序，我们将保持与以前相同的功能，但是另外，如果我们查看控制台，与日志混杂在一起，我们会看到装饰者消息。

## 其他

这是快速查看 ASP.NET 核心中的依赖注入。即使不深入，也足以为即将到来的事情做好准备，我们将在此过程中根据需要引入更多相关概念。

使用 Autofac 对于介绍如何替换内置容器是很好的，但是我并不完全喜欢它的 API，我可能会在将来替换它。

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode005)。

请发送您的任何反馈，以便下一篇帖子/视频可以更好，甚至调整到更有趣的主题。

谢谢你的来访，西阿兹！