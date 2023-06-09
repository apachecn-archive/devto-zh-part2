# 独立烤面包机通知

> 原文：<https://dev.to/georgehanson/dependency-free-toaster-notifications-3hh3>

如今，由于像网络套接字这样的网络技术的使用越来越多，网络应用变得越来越智能。我们现在可以使用 JavaScript 编写交互式和直观的系统。web 应用程序经常需要通知用户一些事情。这可能是他们刚刚执行的操作成功了，或者他们可能是在通知用户一条他们需要注意的重要信息。这些可以通过烤面包机通知来完成。

## 什么是烤面包机通知？

多士炉通知是出现在用户浏览器中的小型弹出式通知。它们通常用不同的颜色来代表不同的结果，比如红色代表失败，绿色代表成功。这些类型的通知对用户来说非常好，原因有很多。首先，它们是非侵入性的。它们不会过多地分散用户对页面内容的注意力，而像 sweetalert box 这样的东西会。此外，它们允许用户继续使用应用程序，而不必执行操作来继续；例如点击“确定”按钮。

## 引见，干杯！

无耻的插在这里， [Toastify](https://github.com/georgehanson/toastify) 是我发布的一个开源的烤面包机通知包。现在你可能会说，“有很多烤面包机通知包”…你是对的！那么我为什么要创造它呢？嗯，我主要是寻找一个没有依赖性的。当时我正在做一个 Vue。JS 应用程序，然而我们正在开发的另一个应用程序是用 Angular 编写的。这两个应用程序都没有使用 jQuery，所以我想要一个可以轻松跨两者工作的东西…瞧！Toastify 诞生了。它是用 typescript 写的，不需要任何依赖！那么，我们开始吧。

## 安装和设置

您可以从 npm.js 存储库中安装该软件包。为此，您可以运行以下命令之一:

`yarn add toastify`

或

`npm install --save toastify`

安装完成后，您需要将这个包导入到您的应用程序中。您可以通过以下方式之一完成此操作:

`Import Toastify from 'toastify'`

或

`const Toastify = require('toastify')`

## 配置

有许多配置选项可以设置。这些是位置、延迟、元素和速度。为了配置这些选项之一，您只需调用`setOption`方法，将键和值作为参数传递。设置配置选项的示例如下所示。

`Toastify.setOption('delay', 5000)`

关于其余选项的文档，可以在这里的 [GitHub 库](https://github.com/georgehanson/toastify)找到。

## 渲染通知

目前，支持四种不同的通知级别。它们是默认、成功、信息、警告和错误。要在页面中呈现通知，只需运行下面的代码`Toastify.success('Title', 'this is the body of the notification')`。如您所见，该方法接受两个参数，一个用于标题，一个用于正文。

如果您有任何问题，请随时在 [Twitter](https://www.twitter.com/gwhdev) 上与我联系