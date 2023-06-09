# 角度建筑:概述与概念

> 原文：<https://dev.to/charumalikcs/angular-architecture-overview-and-concept-n8f>

Angular 是用 TypeScript 编写的，它将核心功能和附加功能实现为一组 TypeScript 库。这些被导入到 Angular 应用程序中。基本上，Angular 是一个框架，也是一个用 HTML 和 TypeScript 构建客户端应用程序的平台。
[![Angular Architecture](img/335915b3d95e2dd2a750081e7b808014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Slyklyc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xzsf2dx9w8dmp41jk9z.png) 
Angular 是全球 web 开发者首选的领先框架之一。因此，这是增加您的编程能力的一个有利可图的选择。这里有一些 [**最佳角度教程**](https://hackr.io/tutorials/learn-angular) 来启动你的角度之旅，并磨练你现存的角度技巧。棱角分明的建筑并不难理解。特别是，当你熟悉了相同的基础上的各种概念。因此，在总结这一切之前，让我们先详细了解一下底层概念。

**任何角度 App 的 7 个组成要素**

角度应用有几个部分。任何有角度的 app 都由 7 个基本成分组成。这些是:

1.  成分
2.  模板
3.  [计]元数据
4.  数据绑定
5.  指令
6.  服务 7。依赖注入

**组件**

任何 Angular 应用程序都必须至少有一个组件。这被称为[根组件](https://stackoverflow.com/questions/44577844/what-goes-in-your-root-component)。它将组件层次结构与页面 DOM(文档对象模型)连接起来。不管 Angular 应用程序有多少个组件，每个组件都定义了一个包含应用程序和数据逻辑的类。
这仅仅意味着一个组件在应用程序中扮演什么角色取决于该组件的类别。每个类都与一个 HTML 模板相关联，该模板定义了目标环境中的一个视图。

紧接在 [@Component () decorator](https://stackoverflow.com/questions/40983890/what-does-component-decorator-exactly-do) 下面定义的类被标识为组件。此后，装饰者向应用程序提供模板(下面讨论)和相关的特定于组件的元数据(也在下面讨论)。

**什么是装修工？**

装饰器是一组独特的函数，用于修改 JavaScript 类。Angular 附带了大量定义好的 decoratorss，每个 decorator 都将一种特定的元数据附加到使用它们的类中。它只是让系统知道这些类是什么以及它们应该做什么。

用[托德格言](https://toddmotto.com/angular-decorators)更好地了解装修工。

**模板**

顾名思义，这些是 Angular 应用程序的元素，它们将 HTML 与 Angular 标记结合在一起，能够在将 HTML 元素投影到屏幕上之前修改它们。模板利用管道来改善用户体验。管道通过转换显示值来实现这一点。这仅仅意味着管道可以用来根据用户的地区添加一个单位，比如时间和货币。Angular 附带了过多的预定义管道。但是，也可以定义自己的管道。下面是[如何](https://alligator.io/angular/custom-pipes-angular/)！

**元数据**

如何处理类完全取决于元数据，就这么简单。类装饰器用于将元数据附加到类上。任何附加了@Component class decorator 的类都被称为组件类。为了提供 Angular 创建组件所需的必要信息，类装饰器使用了配置对象。一些配置选项包括指令、选择器和 templateURL。

**数据绑定**

绑定标记负责将应用程序数据与 DOM 连接起来。有两种类型的数据绑定，即:

**事件绑定**–允许应用程序在目标环境中响应用户输入。它通过更新应用程序数据来做到这一点

**属性绑定**–允许将从应用程序数据计算出的值插入到 HTML 中

DOM 中的变化，就像用户的选择一样，反映在程序数据中。这就是所谓的[双向数据绑定]。这是通过一个 [ngModel 指令](https://angular.io/api/forms/NgModel)实现的。

**指令**

模板指令负责提供逻辑。在显示视图之前，Angular 评估指令并解析模板中的绑定语法，以修改 DOM 和 HTML 元素。这是根据程序数据和逻辑完成的。有 3 种类型的指令:

**属性指令**–修改组件、元素或其他指令的行为或外观

**组件**–这些是带有模板的指令

**结构指令**–通过添加或删除 DOM 元素来修改 DOM 布局

**服务**

当需要一些与特定视图无关的数据或逻辑时，会创建一个服务类。此外，这些数据或逻辑需要跨组件共享。

服务类定义紧接在[@ injectible()decorator](https://blog.ninja-squad.com/2016/12/08/angular-injectable/)之前。它的作用是提供元数据，这允许服务以依赖的形式注入到客户机组件中。

**依赖注入(DI)**

简单地说，DI 使得组件类保持精简和高效。这使这些类能够委托任务，例如从服务器获取数据、直接记录到控制台，以及验证用户对服务的输入。

**其他重要概念**

除了典型角度应用的 7 个基本部分之外，还有两个概念在起作用。这些是模块和路由，分别描述如下:
模块

每个 Angular 应用程序都有一个名为 AppModule 的根模块。它提供了启动应用程序的引导机制。通常，角度应用程序包含几个功能模块。

虽然 [NgModules](https://angular.io/guide/ngmodules) 不同于 JavaScript (ES2015)模块，但前者是后者的补充。尽管如此，ng module 允许分别从其他 ng module 导入和导出功能。这类似于 JavaScript 模块。
ng module 的目的是声明一组组件的编译上下文。这些组件可能属于应用程序域或工作流。为了形成功能单元，NgModule 可以将其组件与相关代码相关联，如服务。

为了便于开发复杂的应用程序，代码可以组织成不同的功能模块。此外，这样做有助于设计可重用性，并从延迟加载中受益，延迟加载可以最大限度地减少应用程序启动时加载代码所需的时间。

**路由**

路由器 NgModule 有助于在 Angular 应用程序中定义许多应用程序状态和视图层次结构之间的导航路径。这个特殊的 NgModule 模仿了流行的浏览器导航约定，即:
在浏览器的地址栏中输入一些 URL 来导航到相应的页面

点击网页上的链接允许浏览器导航到新的网页
点击浏览器中的后退和前进按钮对应于向后和向前导航，以各自的顺序，通过浏览器历史
而不是页面，[路由器](https://angular.io/guide/router)将类似 URL 的路径映射到视图。只要用户执行某个操作，路由器就会拦截浏览器的行为，并通过隐藏或显示视图层次结构做出响应。路由器如何解释链接 URL 取决于应用的视图导航规则和数据状态。

路由器也利用了[延迟加载](https://en.wikipedia.org/wiki/Lazy_loading)。如果路由器发现定义了当前应用状态所需的某些特定功能的模块还没有被加载，则这样做。

路由器在浏览器历史记录中记录活动。这允许后退和前进按钮工作。当用户单击按钮或从下拉框中选择内容时，导航到新视图就完成了。或者，它可能是对某个来源的某个其他操作的响应。

[导航路径](https://www.jvandemo.com/the-7-step-process-of-angular-router-navigation/)与用于定义导航规则的组件相关联。导航路径使用类似 URL 的语法，它与程序数据集成在一起。这类似于模板语法如何将视图与程序数据集成在一起。

此后，根据用户输入和定义的访问规则，应用程序逻辑来选择显示哪些视图和隐藏哪些视图。

这一切是如何运作的！

NgModules 是任何 angle 应用程序的基本构建块，因为它们为组件提供了编译上下文。任何角度应用都由一组 NgModules 定义。这些 NgModules 将相关代码收集到功能集中。一个 Angular app 至少有一个根模块，支持自举和几个[功能模块](https://angular.io/guide/feature-modules)。组件定义视图。这些是根据程序逻辑和数据选择和修改的屏幕元素集。

此外，组件利用提供与视图不直接相关的特定功能的服务。服务提供者作为依赖项被注入到组件中。这使得代码高效、模块化和可重用。

组件和服务都是类。装饰者标记它们的类型，并提供决定如何使用它们的元数据。一些组件类的元数据将其与模板相关联，所有这些都是为了定义视图。

模板将普通的 HTML 代码与 Angular 指令以及绑定标记结合在一起，这允许 Angular 在显示之前修改 HTML。服务类的元数据提供了 Angular 需要通过 DI 提供给组件的信息。

这就概括了角度建筑的概念。要更好地理解相同，实践是关键。这将有助于你对角度应用的工作原理有一个清晰的理解。