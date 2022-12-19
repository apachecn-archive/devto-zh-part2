# Blazor 第 22 周简介

> 原文：<https://dev.to/rembou1/an-introduction-to-blazor-week-22-49p0>

# 布拉索简介

## 什么是 Blazor？

(本帖基于 Blazor 0.3)

简单来说:Blazor 就像 vuejs/angular/react js……但是你用 C#用 Razor 语法编码:)

## 主诣

Blazor 围绕以下概念进行阐述:

*   App Root(没有正式名称) :主要命名为“index.html”，这是用户加载的第一个文件，它将包含以下内容[我的根文件](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Client/wwwroot/index.html)
    *   html 最小标签(html/标题/正文)
    *   外部引用:您需要从外部提供者(如 jqueryui 或 toastr)获得的 js / css 文件
    *   将在其上呈现页面的根标记
    *   用于加载 blazor 脚本的脚本标签
*   App.cshtml:配置你的 App 的临时文件(默认只有路由器扫描在这里)[我的 App.cshtml](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Client/App.cshtml)
*   组件:一个 razor 模板，它是管理页面一部分的 c#代码。
*   页面:就像一个组件，但是当用户在其浏览器上加载 url 时，它将在应用程序主体上创建
*   互操作函数:你可以在一个 js 函数和一个 c#函数之间创建链接(在这两个文件中定义 [C#端](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Client/Services/JsInterop.cs)和 [JS 端](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Client/wwwroot/index.html)
*   入口点[我的文件](https://github.com/RemiBou/Toss.Blazor/blob/master/Toss/Toss.Client/Program.cs)就像你定义的 ASP.NET 核心 app 中的 program.cs 一样:
    *   依赖注入配置
    *   特定于框架的设置(这里我只设置了将被页面内容替换的根应用程序根标签的名称)。

你可以在我的 TOSS 项目[这里](https://github.com/RemiBou/Toss.Blazor)查看所有这些想法

## Visual Studio 集成

你至少需要 Visual Studio 15.7 和 Blazor 语言服务[这里](https://go.microsoft.com/fwlink/?linkid=870389)。Visual Studio 提供了以下关于 Blazor 的工具:

*   2 个项目模板
*   页面/组件上的代码着色
*   页面/组件上的代码自动完成
*   项目建筑
*   通过现有的 IIS Express 集成，您可以在本地托管您的项目，并在您的开发计算机上测试它。

## 它是如何工作的？(或者我理解的工作方式)

*   当您构建时，所有的组件/页面都被翻译成纯 C#类(您可以在您的项目/obj 文件夹中找到它们)
    *   所有这些类都与微软有交互。这是一个 html 标记编写器
    *   当组件或页面的视图模型发生变化时，将调用 BuildRenderTree，然后更新内容
*   所有这些课程都是有针对性的。网络标准 2.0
*   当浏览器加载 index.html 时，它会加载 blazor.js 这个库
    *   加载您的项目入口点
    *   加载它的依赖项(所有目标。网络标准 2.0)
    *   如果浏览器不支持，则初始化 web 程序集
    *   加载 mono.wasm，一个使用 Web Assembly 的平台实现(不要问我更多，我正在学习 Blazor 的过程中试图理解它)，这样你的代码和依赖项就可以在浏览器上执行，并与 dom 交互

## 结论

目前就这些，下一步我将更深入地介绍 Blazor 的一些功能，或者展示如何实现一些基本的功能，如身份验证或 CSRF 保护。

## 参考/链接

*   [http://blog.stevensanderson.com/2018/02/06/blazor-intro/](http://blog.stevensanderson.com/2018/02/06/blazor-intro/)
*   [https://blazor.net](https://blazor.net)
*   [https://github.com/aspnet/Blazor](https://github.com/aspnet/Blazor)
*   [http://www . mono-project . com/news/2017/08/09/hello-web assembly/](http://www.mono-project.com/news/2017/08/09/hello-webassembly/)