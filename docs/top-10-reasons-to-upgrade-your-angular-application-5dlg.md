# 升级角度应用的 10 大理由

> 原文：<https://dev.to/jsguru_io/top-10-reasons-to-upgrade-your-angular-application-5dlg>

[![](img/ea06a8e37636b69d7bdb77c07aa2bbfc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ESQzqx9f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AbRlKDjxqkQTfgEz40jdftA.png)

Angular 是一个流行的 JavaScript 框架，用于创建 web、桌面和移动应用程序。它由谷歌建立和维护。

Angular 5(代号 pentagonal-donut)于 2017 年 11 月 1 日发布，这是一个包含新功能的主要版本，专注于使您的 Angular 应用程序更快、更小、更易用。

那么，有了所有这些新功能，你应该升级到 Angular 5 的 10 大理由是什么？

### **1)表现**

Angular 5 将使你的应用程序更小更快。Angular CLI v1.5 附带了一个默认打开的构建优化器，并且 Angular 编译器得到了改进，可以更快地构建和重建。通过删除 AST 类来减小包的大小，只发出更改过的文件进行增量编译等等。提前编译器是默认开启的，它比 Angular 4 快得多。它不同于实时编译，因为它在浏览器下载和运行它之前将 Angular Typescript 转换为 JavaScript 代码。实时编译器在运行时编译应用程序。使用 AOT 渲染速度更快，下载文件更小，并且可以更早地发现模板错误。这使得 Angular 5 的开发和生产速度更快。

### **2)渐进式网络应用**

渐进式 web 应用程序(PWA)是一种使用现代 web 功能向用户提供类似应用程序的体验的 Web 应用程序。这是一种结合了最好的网络和移动应用的体验。

渐进式网络应用程序适用于每个用户，不管他们使用什么浏览器，它反应灵敏，由于服务人员的帮助，它总是最新的，如果通过 HTTPS 提供服务，它也是安全的，并且一直感觉像一个真正的应用程序！

现在，渐进式 web 应用程序的开发是一个复杂的过程。在开发和部署过程中，需要注意旧版本的缓存和交付都不会受到影响。

随着 Angular 5 的出现，这种情况发生了变化。渐进式 web 应用程序的开发应该简化，这样它们甚至可以默认创建。

通过 Angular-CLI，Angular 能够自己创建配置和代码。本质上，这允许创建具有本地移动应用的特征的移动网络应用，例如离线能力、推送通知和特定平台的开始菜单中的应用标志。

### **3)构建优化器**

Angular 团队专注于让 Angular 5 更快、更小、更易用。在 Angular 5 中，使用 Angular CLI 创建的生产构建现在将默认应用构建优化器。构建优化器包含在 Angular CLI 中。

构建优化器删除应用程序中不需要的部分，并从应用程序的运行时代码中删除角度装饰器。装饰器由编译器使用，在运行时不需要，可以删除。

这减小了 JavaScript 包的大小，提高了应用程序的启动速度！

### **4) HttpClient**

在 4.3 版本之前，@angular/http 用于发出 http 请求。HTTP 现已弃用，现在建议在所有应用程序中使用 4.3 版中附带的@angular/common/http 包中的 HttpClient API。

一些功能包括

● JSON 是假定的默认值，不再需要显式解析

●拦截器允许中间件逻辑插入到管道中

●请求上传和响应下载的进度事件。

### **5)新路由器生命周期事件**

路由器有新的生命周期事件，允许您跟踪路由器从开始运行防护到完成激活的周期。这些事件可以用于诸如当孩子正在更新时在特定路由器出口上显示微调器，或者测量防护器和/或解析器的性能。

这个新的生命周期事件是 GuardsCheckStart、ChildActivationStart、ActivationStart、GuardsCheckEnd、ResolveStart、ResolveEnd、ActivationEnd、ChildActivationEnd。

### **6) CLI**

从 Angular CLI 版本 1.5 开始，添加了对 Angular 5 的支持，默认情况下 CLI 会生成 v5 项目，并且还会打开构建优化器。默认情况下，如果你使用 Angular 5 并在 AOT 进行构建，那么构建优化器插件将会应用到你的构建中。

构建命令

```
ng build --prod --build-optimizer 
```

现在简化为

```
ng build --prod 
```

如果您使用的 TypeScript 版本不是 Angular 版本的推荐版本，Angular CLI 也会发出警告。你可以用以下方法解除警告(只有当你相信自己并且知道自己在做什么的时候)

```
ng set typescriptMismatch = false 
```

### **7)表格**

为了提高性能，我们现在可以指定什么时候应该执行验证器。每次值改变时，验证很可能会在每次击键时执行！

在 Angular 5 中，表单能够决定何时通过模糊或提交来更新字段或表单的有效性和值，而不是在每次输入事件时更新。这可以极大地提高性能！

为了提高性能，现在可以指定何时在表单中执行验证器。

### **8)管道**

在以前的版本中，Angular 依赖浏览器使用浏览器 i18n API 提供日期、数字和货币格式。用户在不同的浏览器上看到不一致的结果。

Angular 5 带有新的日期、数字和货币管道。

管道依靠 [CLDR](http://cldr.unicode.org/) 为您想要支持的任何地区提供地区支持和配置。

这里有一个[文档](https://docs.google.com/spreadsheets/d/12iygt-_cakNP1VO7MV9g4lq9NsxVWG4tSfc98HpHb0k/edit%23gid=0)比较了 v4 和 v5 的管道。

你还没准备好接受新管道吗？可以在 CommonModule 之后导入 DeprecatedI18NPipesModule。

### **9)增量构建**

对 Angular 编译器做了很多改进，使它更快，现在它可以更快地构建和重建。当使用*–AOT*和 *ng serve* 命令时，提前和增量构建是可能的。这可能会在 Angular CLI 的未来版本中默认使用。

### **10)更好的 Typescript 和 RxJS 支持**

RxJS 版本 5.5 引入了一种使用 RxJS 的新方法，称为“可允许操作符”。现在可以从 rxjs/operators 导入任何操作符。这些新操作符消除了以前导入操作符的方法中存在的副作用和代码分裂/树抖动问题。默认情况下，新的 Angular CLI 将拉这个版本，并将大大节省包的大小！

[![](img/bf82d5e73b2f49a068810b9a9e6101b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dAhW1auw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AwDkdwUhMM555aZ8J.png)

### 是否要将应用升级到 Angular 5？

Angular 团队开发了一个[漂亮的工具](https://angular-update-guide.firebaseapp.com/)让升级变得尽可能简单，或者**给我们发消息**到 [info@jsguru.io](mailto:info@jsguru.io) 。

### 出发前…

如果你喜欢读这篇文章，请**分享**它。你应该看看我们的其他出版物，你可能也会喜欢它们！我们不时地写一些关于软件开发、技巧和诀窍，以及如何成为一个更好的开发人员和商业人士的文章。加入我们不断改进的旅程吧！

在[脸书](https://www.facebook.com/jsguruio/)、[推特](https://twitter.com/jsguru_software)、 [LinkedIn](https://www.linkedin.com/company/jsguru) 、 [Medium](https://medium.com/jsguru) 或 [DEV.to](https://dev.to/jsguru_io) 上关注我们。

*原载于*[*jsguru . io*](https://jsguru.io/blog/Top-10-reasons-to-upgrade-to-Angular-5)*。*