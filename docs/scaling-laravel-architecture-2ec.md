# 缩放 Laravel 架构

> 原文：<https://dev.to/mattnmoore/scaling-laravel-architecture-2ec>

Laravel 附带了一个很好的起始目录结构，它可以支持您的整个项目，这取决于项目的大小。然而，如果你注意到找到你想做的代码变得越来越难，你可能要后退一步，重新思考你的应用程序的架构。

我在 Laravel 世界看到的最流行的模式是领域驱动设计和六边形架构。虽然我绝不是 DDD 专家，但我发现它在拉勒维尔的模式内运行良好，只要你不试图完全遵循它。

例如，正如这篇博文所指出的,[雄辩大大违反了 DDD 原则。我认为这里的错误是试图实现“纯粹的”DDD，而不是将其作为一个指导方针。](http://lorisleiva.com/conciliating-laravel-and-ddd/)

雄辩术是一种活跃的记录形式，因此本质上与 DDD 不兼容。这不是坏事！我宁愿倾向于这一点，而不是与之抗争，拥抱雄辩术给你的力量和 DDD 的好处。

我发现这些 Laravel 组件非常好地映射到下面的领域驱动设计层。

*   控制器/控制台命令类别-演示
*   作业-应用程序
*   存储库-基础设施
*   模型-领域

## 演示

控制器负责 DDD 的表示层。无论您构建的是一个 API 还是一个返回 HTML 视图的应用程序，您的控制器都负责将这些内容呈现给用户。自定义 Artisan 命令类也可以被视为表示层的一部分。

## 申请

域驱动代码库的应用层负责协调域和基础设施层，以在应用程序中执行高级任务。

这一层我喜欢用 Laravel 的[命令总线](https://laravel.com/docs/5.7/queues#creating-jobs)。我采纳了 PyroCMS 的想法，从此就没有回头。我也见过用网关[表示的应用层，这在本文](http://ryantablada.com/post/two-design-patterns-that-will-make-your-applications-better)中有所概述，但我认为将特性封装到一个单一的作业类中更简洁。

使用作业的一大好处是，您可以从任何表示层调度它们。只要确保使用 dispatch_now()函数，这样它就不会排队。

```
$result = dispatch_now(new CreatePost($request)); 
```

Enter fullscreen mode Exit fullscreen mode

## 基础设施

基础设施层处理与应用程序外部的代码或服务交互的任何事情。这包括电子邮件、API 调用，以及最常见的数据库交互。我非常喜欢在 Laravel 应用程序中与数据库交互的[存储库模式](https://medium.com/employbl/use-the-repository-design-pattern-in-a-laravel-application-13f0b46a3dce)。封装查询逻辑和模型检索是减少错误和促进数据库交互代码重用的可靠方法。

## 域

领域层包含所有业务逻辑，是应用程序的核心。由于您的领域模型知道您的数据库，所以在使用雄辩时，这一层可能会变得模糊。如果我们忽略这个方面，只使用存储库与数据库交互，我相信我们可以满足充分分离这些关注点的需求。

我见过黑客在雄辩中禁用数据库调用，但我认为这是不必要的。我发现在文档/代码审查级别将这些标准融入到项目中就足够了。

除了雄辩之外，另一种选择是使用拉勒韦尔主义。这为您提供了使用实体和数据映射器为基础设施层创建真正的域层所需的一切。在处理真正大型的应用程序时，这种方法有很多好处，但是对于较小的应用程序来说，这种方法可能有些矫枉过正。

我通常喜欢雄辩，尤其是当你拼凑一个原型或概念证明时，它提供的速度，我相信它甚至可以很好地工作在大型应用程序中。然而，我已经亲身经历了它的一些缺点，并且认为在某些情况下，诸如数据映射器之类的替代品是完全有效的选择。重要的是，你要始终权衡你的选择，并为这项工作选择正确的工具。

[这里有一篇精彩的文章，诚实地看待这两种模式及其权衡](https://www.thoughtfulcode.com/orm-active-record-vs-data-mapper/)

好的，那么这在 Laravel 应用程序中实际上是什么样子的呢？让我们来看看我在按照这个架构开始一个新项目时通常采取的步骤。

## 调教

假设我们想要建立一个包含博客和项目部分的个人网站。姑且称之为我们的投资组合。

注意:这么小的站点在默认的目录结构和基本的 Laravel 架构下会工作得很好，但是现在忽略它。

使用 composer create-project 或 laravel CLI 下载 Laravel 的新版本，如文档中所述[。](https://laravel.com/docs/5.7/installation)

使用
命名您的应用程序

```
php artisan app:name Portfolio 
```

Enter fullscreen mode Exit fullscreen mode

## 移动核心代码。

您的初始应用程序目录结构应该如下所示:

```
├── Console
│   └── Kernel.php
├── Exceptions
│   └── Handler.php
├── Http
│   ├── Controllers
│   │   ├── Auth
│   │   │   ├── ForgotPasswordController.php
│   │   │   ├── LoginController.php
│   │   │   ├── RegisterController.php
│   │   │   ├── ResetPasswordController.php
│   │   │   └── VerificationController.php
│   │   └── Controller.php
│   ├── Kernel.php
│   └── Middleware
│       ├── Authenticate.php
│       ├── CheckForMaintenanceMode.php
│       ├── EncryptCookies.php
│       ├── RedirectIfAuthenticated.php
│       ├── TrimStrings.php
│       ├── TrustProxies.php
│       └── VerifyCsrfToken.php
├── Providers
│   ├── AppServiceProvider.php
│   ├── AuthServiceProvider.php
│   ├── BroadcastServiceProvider.php
│   ├── EventServiceProvider.php
│   └── RouteServiceProvider.php
└── User.php 
```

Enter fullscreen mode Exit fullscreen mode

我首先喜欢将所有的核心代码移到一个 Core\ namespace 中。这包含了所有 laravel 特定的设置代码，比如主服务提供者、内核等。

为此，在 app/ directory 中创建一个核心文件夹，并将除用户模型之外的所有内容都移入其中。无论是使用 IDE 还是手动，现在都必须调整这些目录中所有类的名称空间。只需在 Portfolio 后添加核心名称空间\就像这样:

```
namespace Portfolio\Core\Providers; 
```

Enter fullscreen mode Exit fullscreen mode

一旦调整了名称空间，我们必须修正一些引用。

*   **bootstrap/app.php**
    修复 HTTP 和控制台内核的单体绑定，以及异常处理程序。

*   **config/app.php**
    修复对应用服务提供商的引用。

*   **app/Core/Http/kernel . PHP**
    修复`CheckForMaintenaceMode`、`TrimStrings`、`TrustProxies`、`EncryptCookies`、`VerifyCsrfToken`中间件的中间件引用，以及`Authenticate`、`RedirectIfAuthenticated`路由中间件。这些引用可以通过简单地在中间件类前面加上`Middleware\`名称空间来简化，例如`Middleware\CheckForMaintenanceMode::class`

*   **app/Core/Providers/routeserviceprovider . PHP**
    修复`$namespace`属性，允许多个控制器目录。我更喜欢将其简化为路由名称空间，在本例中为`Portfolio`

*   **app/Core/Console/kernel . PHP**
    删除对`load()`(第 38 行)的调用，因为命令会在很多地方被声明。我更喜欢使用`$commands`属性显式地声明命令，或者您可以使用较新的控制台路由文件。

现在，您可以从头开始构建您的第一个功能模块了。您的应用程序/目录结构现在应该看起来像这样。

```
Core
    ├── Console
    ├── Exceptions
    ├── Http
    │   ├── Controllers
    │   │   └── Auth
    │   └── Middleware
    └── Providers 
```

Enter fullscreen mode Exit fullscreen mode

## 博客模块

让我们开始博客模块。每个模块都需要一些相同的组件。控制器(表示)、作业(应用程序)、模型(领域)和存储库(基础设施)。我喜欢在每个模块内部松散地遵循默认的 Laravel 结构，就像这样:

```
Blog
├── Http
│   ├── Controllers
│   │   └── PostController.php
│   ├── Requests
│   │   ├── CreateOrUpdatePost.php
│   │   └── DeletePost.php
│   └── Transformers
│       └── PostTransformer.php
├── Jobs
│   ├── EditPost.php
│   ├── RemovePost.php
│   └── WritePost.php
├── Repositories
│   ├── EloquentPostRepository.php
│   └── PostRepository.php
├── Author.php
├── Comment.php
└── Post.php 
```

Enter fullscreen mode Exit fullscreen mode

表示层位于 Http/和 Console/中。在这里你可以找到你的控制器、表单请求和变形器(我推荐[分形](https://fractal.thephpleague.com/))。

应用程序层的大部分可以在 Jobs 目录中找到。您的工作应该做的唯一一件事就是委托给较低的层，比如存储库和服务类。任何商业逻辑都不应存在于此。

存储库目录似乎不言自明，这是您的存储库所在的位置。

模型在模块的根中。我更喜欢这种结构，因为它是大多数业务逻辑应该存在的地方，应该是任何模块中最可见和最容易获得的部分。任何其他服务类也可以放在这里，或者您可以选择将它们放在服务或实用程序名称空间中。

虽然这是一个非常基础的模块，但我相信它展示了如何使用 DD 的原则来构建 Laravel 应用程序的思想。您可以以同样的方式自由地实现任何其他 Laravel 组件，例如事件或侦听器将成为模块根目录中的目录，通知或邮件类也是如此。

如果你的模块开始变得太大，你可以把它分解成子模块，但是我建议你尽一切可能保持你的目录树尽可能的扁平。只有当它是最干净的解决方案时才嵌套！拥有 5 个子模块，每个子模块有一个模型，这可能是多余的。

## 项目模块

我们的“项目”模块在结构上看起来非常相似。

```
Projects
├── Http
│   ├── Controllers
│   │   └── ProjectController.php
│   ├── Requests
│   │   ├── CreateOrUpdateProject.php
│   │   └── DeleteProject.php
│   └── Transformers
│       └── ProjectTransformer.php
├── Jobs
│   └── CreateProject.php
├── Repositories
│   ├── EloquentProjectRepository.php
│   └── ProjectRepository.php
└── Project.php 
```

Enter fullscreen mode Exit fullscreen mode

## 将评论提取到自己的模块中

假设我们想要在项目模块中启用注释。目前，评论代码位于博客模块中，因此我们必须将其重构为一个共享模块。

共享模块可以存在于核心名称空间中，也可以存在于自己的顶层模块中。或者您可能希望创建一个带有实用程序类、特征或子模块的共享模块。这些都是完全正确的，并且将取决于多种因素。

让我们为博客和项目模块创建一个顶级评论模块。

```
Comments
├── Jobs
│   ├── CreateComment.php
│   ├── RemoveComment.php
│   └── UpdateComment.php
├── CommentController.php
├── Comment.php
├── CommentRepository.php
└── EloquentCommentRepository.php 
```

Enter fullscreen mode Exit fullscreen mode

注意模型、控制器和存储库是如何存在于根模块目录中的。这只是个人喜好。如果一个组件只有一种类型，我不会把它放在自己的目录中，因为这只会使它更难找到文件。当一个目录变得太大而不容易查找时，我才开始把东西拆分到它们自己的目录中。这可能不是最好的架构模式，但是到目前为止对我来说效果很好。做你觉得最好的事，但重要的是保持一致！不要凭感觉行事，创建组织文件的规则，以便参与项目的其他人可以复制您的组织。

## 总结

在未来，我想更深入地研究这种架构模式在代码级别的表现，但我希望我已经阐明了如何使用 Laravel 有效地组织一个不断增长的项目。

我目前正在阅读[领域驱动设计](https://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)，并推荐给任何想了解更多关于 DDD 的人。我绝不是这方面的专家，这是我一直在努力改进的地方。如果你有任何建议或者想指出我所展示的任何东西的缺陷，请在评论中自由地提出。感谢阅读！

帖子[Scaling Laravel Architecture](https://mattdoescode.com/scaling-laravel-architecture)最早出现在 [Matt Does Code](https://mattdoescode.com) 上。