# 原因:开始使用

> 原文：<https://dev.to/jlewin_/reasonml-getting-started-53gi>

在本教程中，我们将使用 Reason 构建一个小型天气应用程序。在页面的[底部有一个指向源代码的链接。本教程假设您对 React 有基本的了解，因为我们将使用 React 绑定来构建应用程序。如果你以前没有用过 React，](#reason-app-demo)[这篇文章](https://dev.to/tylermcginnis/a-comprehensive-guide-to-reactjs-in-2018--4nbc)是一个很好的起点。

[![ReasonML](img/93d0325df1da63cda2539ccdd0132ece.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WCWlyt_9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rndtcngsysf696cvb0wf.png)

## 原因是什么？

Reason 是 OCaml 的一种新语法，由脸书开发，深受 JavaScript 的影响。它有 100%的类型覆盖率，这导致了一个非常强大的类型系统。

Reason 也适合跨平台开发。我们可以使用 BuckleScript 将我们的代码编译成(可读的)JavaScript，从而开放整个 web 平台。多亏了 OCaml，使用 Reason 进行本地开发也成为可能。

此外，Reason 可以访问整个 JS 和 OCaml 生态系统，并提供 React 来构建带有 ReactJS 的 UI 组件。在文档中有一个[有用的页面，更详细地解释了这些优势！](https://reasonml.github.io/docs/en/what-and-why.html)

* * *

## 要求

首先，让我们确保安装了正确的工具。

我们将使用 Create React App 来引导项目。如果你以前没用过，通过运行`npm i -g create-react-app`来安装。我们还需要另外两个包来开始:

*   原因 CLI:原因工具链。[查看安装文件](https://github.com/reasonml/reason-cli#1-choose-your-platform)。
    在撰写本文时，macOS 用户可以通过运行`npm i -g reason-cli@3.1.0-darwin`进行安装

*   BuckleScript: `npm i -g bs-platform`

我还使用了 [vscode-reasonml](https://github.com/reasonml-editor/vscode-reasonml) 编辑器插件。如果你使用不同的编辑器，[查看插件列表](https://reasonml.github.io/docs/en/editor-plugins.html)找到适合你的。

* * *

## 我们的第一个组件

首先，我们将为我们的应用程序创建样板代码:

`create-react-app weather-app --scripts-version reason-scripts`

这为我们提供了一个基本的应用组件: