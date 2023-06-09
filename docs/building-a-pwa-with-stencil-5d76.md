# 使用模板构建 PWA

> 原文：<https://dev.to/techiediaries/building-a-pwa-with-stencil-5d76>

PWAs 或渐进式网络应用程序是一种类似于应用程序的网络体验，为用户提供了许多优势，例如:

*   可靠性
*   接合能力
*   快速性/性能

从谷歌开发者博客中阅读更多关于什么使应用成为 PWA 的信息

你也可以在 SitePoint 阅读[我关于 PWAs 的文章](https://www.sitepoint.com/progressive-web-apps-a-crash-course/)

没有关于 PWA 的更多信息，让我们学习如何使用 Ionic 团队创造的新工具 [StencilJS](https://stenciljs.com/) 来构建一个非常快速的 PWA

Stencil 是一个 web 组件编译器，可用于使用 web 组件构建现代快速的前端 web 应用程序。

对 web 组件这个概念不熟悉？这是一个容易理解的定义

> Web 组件是一组 web 平台 API，允许您创建新的自定义的、可重用的、封装的 HTML 标记，以便在网页和 web 应用程序中使用。定制组件和小部件建立在 Web 组件标准的基础上，可以跨现代浏览器工作，并且可以与任何支持 HTML 的 JavaScript 库或框架一起使用。[来源](https://www.webcomponents.org/introduction)

因此，web 组件是一套现代浏览器，允许开发人员创建自己的 HTML 标签(即，他们可以扩展 HTML 语言)，重复使用它们而无需每次都重新发明轮子，并与其他开发人员共享它们。

一旦你创建了一个 web 组件，你就可以使用它，只要你使用任何标准的 HTML 标签。例如:

```
<my-comp></my-comp> 
```

Enter fullscreen mode Exit fullscreen mode

不用说，你可以不用模板来创建 web 组件，那么为什么要使用模板呢？

Stencil 为您提供

*   一个简单的 TypeScript API 来创建您的组件，然后生成一个符合标准的 web 组件(特别是一个*自定义元素*)
*   一组现代 API，如虚拟 DOM 和 JSX
*   6kb min+gzip 运行时，服务器端渲染
*   React 纤维启发的异步渲染
*   反应式数据绑定
*   与框架无关(如果您愿意，web 组件可以与任何框架一起使用，也可以不与任何框架一起使用)
*   像大多数现代工具一样，实时重新加载和开发服务器
*   并帮助您轻松构建基于 web 标准且没有框架的 PWAs

观看此发布视频了解更多信息

## 你会学到什么？

在本教程中，我们将开始使用模板，学习最基本的，然后下一步，我们将建立一个简单的现实世界的 PWA。

你会学会的

*   如何安装模板
*   如何使用模板和类型脚本创建您的第一个 web 组件
*   如何构建和生成您的 web 组件
*   如何使用模板从头开始构建一个简单的 PWA

## 要求

像当今大多数现代 JavaScript 工具一样，您需要安装 Node.js 和 NPM。

如果他们还没有安装(也许你买了一台新电脑！)直接去 Node.js 的官方网站下载你的操作系统的安装程序。安装过程通常很简单。

## 开始一个新的模板项目

启动一个模板项目只需要从 GitHub 克隆一个官方项目启动程序,然后安装依赖项

因此，到您的终端(或命令提示符)并运行下面的命令(您需要安装 Git)

```
git clone https://github.com/ionic-team/stencil-app-starter my-stencil-pwa 
```

Enter fullscreen mode Exit fullscreen mode

这将创建`my-stencil-pwa`文件夹，并在那里克隆启动项目文件。

您需要在项目的根文件夹中导航并安装依赖项:

```
cd my-stencil-pwa
npm install 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您可以通过运行 *npm* 启动脚本
来启动一个实时重新加载的本地服务器

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

如果您在 Linux 或 Mac 上得到一个与 Node.js ENOSPC 相关的错误(错误没有足够的空间),但您的驱动器上仍然有足够的空间，只需通过运行以下命令增加系统观察器的数量:

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p 
```

Enter fullscreen mode Exit fullscreen mode

本地服务器运行在 [http://localhost:3333/](http://localhost:3333/)

这是项目的目录结构

[![](img/75168b6e08aa522c9d4a2d6b275eb018.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_gT1bnZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.cimg/b97b0a37-7a5e-4e19-8505-28cd778e0e8b.png)

如果您的模具应用程序启动时出现以下错误:**此浏览器禁用此模具应用程序。**就像下面的屏幕截图

[![](img/51c7bd07806702c9d4b336e66a47eeab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wGRam1vV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.cimg/2639802d-c94a-4917-bc8d-485526bcd3cc.png)

这个错误是由于你的浏览器(在我的例子中是 Firefox)还不支持 *ES 模块导入*

你只需要运行下面的命令来代替

```
npm run dev --es5 
```

Enter fullscreen mode Exit fullscreen mode

您现在应该能够看到您的应用程序启动并运行

[![](img/682681ff02eaaf6e1ec98c388b72453e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J43aQU9c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://screenshotscdn.firefoxusercontent.cimg/8819f2cf-8b85-414a-a18b-7233f76bed28.png)