# 从单一代码库开发 iOS、Android 和 Web 应用

> 原文：<https://dev.to/mrisek/develop-ios-android--web-apps-from-a-single-codebase-1n1a>

现在，使用移动和 web 应用程序共享代码来设置 Angular 项目是一个非常简单的过程。在过去，软件开发人员必须在 web 和原生移动项目开发期间创建至少两个独立的项目。今天，我们需要的一切就是 Angular CLI 和 NativeScript Schematics，这是一个集合，可以从同一个项目中构建 web 和移动应用程序。快速入门的唯一先决条件是安装 Angular CLI (v6.1.0 或更高版本)和最新版本的 NativeScript Schematics。

**NativeScript 共享项目**

共享项目是指 Android、iOS 和 Web 应用程序的源代码位于同一个地方。特定于平台的代码被划分到不同的文件中，我们的目标是在应用程序之间共享尽可能多的代码。可以共享的是导航的路线、公共业务逻辑的服务和公共组件行为的组件。另一边，不能共享的是视图，CSS 和 HTML 标记，还有 NgModules。视图肯定会不同，因为我们经常在移动和 Web 应用程序中使用各种用户界面组件。考虑到所有提到的因素，共享项目中共享代码的比例可能达到 70%。

**移动&网络项目多样性**

如果我们想确保移动和 web 应用程序的最终外观是不同的，但也保持了引擎盖下的共同逻辑，就有必要做下一步。Web 模板应该命名为 foo.component.html，而我们的 Android 和/或 iOS 应用程序的本地脚本模板应该是 foo.component.tns.html，所以移动和 web 视图将被分开。这样，我们将能够通过异步管道加载数据，或者在两个示例中执行相同的业务逻辑方法。通过 Visual Studio 代码中可用的代码片段，可以轻松快速地生成不同的文件，并且您还可以为每个移动平台(iOS 和 Android)开发独特的功能。

**安装**

```
# first you should install Angular CLI
npm i -g @angular/cli

# after that, you can install NativeScript Schematics
npm i -g @nativescript/schematics 
```

Enter fullscreen mode Exit fullscreen mode

**使用 Angular CLI 创建新项目**

```
# classic Shared Project
ng new --collection=@nativescript/schematics project-name --shared

# classic Shared Project (shorter)
ng new -c=@nativescript/schematics project-name --shared

# Shared Project with SASS styling
ng new -c=@nativescript/schematics sass-project --shared --style=scss

# Shared Project with CSS styling (without standard NativeScript Core Theme)
ng new -c=@nativescript/schematics no-theme-project --shared --no-theme

# Shared Project with SASS styling (without standard NativeScript Core Theme)
ng new -c=@nativescript/schematics sass-no-theme-project --shared --style=scss --no-theme 
```

Enter fullscreen mode Exit fullscreen mode

**造型和主题**

默认情况下，CSS 是共享项目中的标准样式格式，它由两个主要部分组成。它们是用于移动设备的 app.css 和用于 web 应用程序的 styles.css。然而，如果我们想使用 SASS 而不是 CSS 方法，所有必要的文件将会在您创建新项目时生成，如果我们声明 sass-project。我们还必须记住，NativeScript 核心主题是在每个新创建的项目中自动生成的，我们可以在创建新的共享项目时通过使用- no-theme 标志来绕过它。之后，package.json 中的所有标准主题配置都将被删除，它们在 NativeScript 样式表中的引用也将被删除。

**构建并运行您的 iOS、Android 或 Web 应用**

```
# build web app
ng serve

# build web app & open it in default browser
ng serve -o

# run native iOS application
tns run ios --bundle

# run native Android application
tns run android --bundle 
```

Enter fullscreen mode Exit fullscreen mode

**总结**

Angular 向我们展示了一种在本地移动和 web 应用程序之间共享代码的令人兴奋的新方式。这使我们能够轻松地共享业务逻辑，并提供了一种简单直观的方法来区分 iOS、Android 和 Web 应用程序之间的特定代码。这是共享项目基本特性的概述。您可以尝试将现有的 Angular Web 项目转换为代码共享项目，并阅读下面的链接。干杯！

[在网络和移动设备上运行的应用](https://blog.angular.io/apps-that-work-natively-on-the-web-and-mobile-9b26852495e7)

[迁移现有的 Angular Web 项目](https://docs.nativescript.org/angular/code-sharing/migrating-a-web-project)