# Pudl 简介:一个易于使用的基于 JavaScript 的静态站点生成器

> 原文：<https://dev.to/maedahbatool/introducing-pudl-an-easy-to-use-javascript-based-static-site-generator-3h0d>

今天，我非常兴奋地发布我的开发友好的入门工具包 [`pudl`](https://github.com/MaedahBatool/pudl) 。在这篇文章中，我将分享什么是`pudl`，构建这个开发工具背后的动机是什么&哲学，以及我如何开始直到它发布的故事。让我们先从介绍开始…

## 🐶 [`pudl`](https://github.com/MaedahBatool/pudl) 介绍

[![](img/5918a8e81ba9f611d2e68021ad29143c.png) ](https://github.com/MaedahBatool/pudl) [pudl](https://github.com/MaedahBatool/pudl) 是一个基于 JavaScript 的静态站点生成器，用于在教学/演示时快速制作令人敬畏的小型静态演示原型。打包在 [pudl](https://github.com/MaedahBatool/pudl) 中，您将找到 **Gulp** 、 **Sass** 和 **Pug** 的工作流来帮助您开始。它的目标是初学者和专业人士，他们都希望为任何静态网站获得一个坚实的开端。所以基本上， [pudl](https://github.com/MaedahBatool/pudl) 是 Gulp 的一个简单实现，它有助于执行以下任务:

*   ⚡️使用 Pug 生成 HTML 文件
*   🎯处理 Sass 到 CSS 的转换
*   🔥Live 用 BrowserSync 重新加载浏览器
*   👊监视文件的更改

## 🎩这一切是如何开始的？

在过去的 9 年里，我一直在 WordPress 工作，在这段时间里，我经历了不同的角色，比如从*内容战略家*到*项目经理*以及一个有抱负的*开发者*。但是特别是在过去的两年里，自从我和令人难以置信的 Ahmad Awais(他是开源开发者的忠实拥护者)结婚后，我一直在处理他的项目的前期开发工作流程。在这段时间里，我遇到了一个非常单调的工作程序，其中包括配置工具，将`pug`渲染为`html`、`sass`渲染为`css`，并将 live reload 与 BrowserSync 集成在一起。此外，我意识到每次我开始任何新项目时，至少有 20%的时间会投入到这个过程中。因此，为了阻止我每天重复发明轮子，并遵循 DRY(不要重复自己)哲学，我决定建立一个具有我需要的正确功能的站点生成器。当我开始的时候，我只想让它满足我的网络开发工作流程的需要。当我继续开发它的时候，我意识到它也可以为其他几个开发人员服务。因此，在这里我给出了 [pudl](https://github.com/MaedahBatool/pudl) ，它是 Gulp 的一个简单实现，用于执行以下任务:

*   ✅帕格到 HTML 的转换
*   ✅ Sass 到 CSS 的转换
*   ✅观察文件的变化
*   ✅错误处理和通知消息。

## 🔥入门指南

开始使用 [pudl](https://github.com/MaedahBatool/pudl) 太简单了。只需 4 个简单的步骤，您就可以为您的开发项目建立一个基本的 web 架构。最棒的是，你不必重复。

### →步骤#0:安装节点& NPM 包管理器

如果你对`Node.js`、JavaScript 和`npm`包的世界完全是初学者，你需要做的就是去节点的网站[下载并在你的系统上安装](https://nodejs.org/en/download/)节点。这将同时安装`Node.js`和`npm`，即节点包管理器——node . js 的命令行界面。您可以通过打开您的终端应用程序并键入以下内容来验证安装...