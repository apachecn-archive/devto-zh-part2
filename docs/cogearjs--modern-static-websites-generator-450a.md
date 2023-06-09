# 齿轮。JS —现代静态网站生成器

> 原文：<https://dev.to/codemotion/cogearjs--modern-static-websites-generator-450a>

**齿轮。JS** 是一个用[节点构建的静态网站生成器。JS](https://nodejs.org) (9.x 或更高版本)并基于 [Webpack](https://webpack.js.org) (v4.6)。

它受到 Jekyll 和其他人的启发，但建立在最新的前端技术之上。

在`development`模式下提供令人敬畏的热重装体验。

[https://www.youtube.com/embed/nDga67P6lag](https://www.youtube.com/embed/nDga67P6lag)

## 特性

*   🖥 **现代技术栈**用捆绑的脚本和风格构建现代静态网站。快速制作原型并立即部署到服务器上。使用**任何现代前端栈** ( **webpack** 捆绑)–[Vue。JS](https://vuejs.org) 、[反应](https://reactjs.org)、[角度](https://angular.io)、[余光](https://emberjs.org)等。
*   🚀**极快且可靠**每秒执行近 1.000 页(取决于页面内容和原始计算机处理器能力)。服务器每秒可以处理成千上万的请求来为静态文件提供服务(即使是在很小的 VPS 上)。
*   📦**对于任何主机**不需要**任何数据库**(数据存储在平面文件中)并且与**任何主机**一起工作(因为它产生*静态 html 和资产文件*)。
*   🚚**部署内置** [创建预置](https://cogearjs.org/docs/deploy)并通过 FTP、SFTP 甚至`rsync`将你的站点更新到服务器。
*   🔓**安全。不需要更新**,忘记那些来自普通 CMS 的烦人的定期更新吧。对于黑客攻击来说，这是 100%安全的 T2，因为部署到服务器后没有后端。
*   ***自由。开源**免费使用。满足任何需求。永远。*

 *[Github Pages](https://pages.github.com) (或任何类似的项目)你可以免费托管生成的网站**。**

 **### 它可以用来做什么:

*   快速现场原型制作
*   投资组合网站
*   公司网站
*   产品网站
*   个人博客
*   艺术家或音乐家网站

任何有管理员生成的内容的网站。

> 可以通过 [Github](https://github.com) 提供多用户内容管理。只需将您的源代码存储在存储库中，接受来自其他用户的拉请求，并在提交后构建一个站点(可以自动化)。

使用 [Firebase](https://firebase.google.com) 或其他任何*后端*，在*编写任何 lang* ( **PHP** ， **Ruby** ， **Python** ，**节点。JS** )或者甚至像 **WordPress** 这样的 CMS，借助像 [Vue 这样的现代前端技术。JS](https://vuejs.org) 或 [React](https://reactjs.org) ，可以变成更动态的站点，如**电子商务**、**产品目录**等。

### 不能用于什么:

*   论坛
*   社交网络
*   闲谈

或者任何其他具有大量用户生成内容的站点类型，这些内容严重依赖于数据库的使用和动态生成的页面。

当然，你可以尝试，但它必须是现代的 [SPA](https://en.wikipedia.org/wiki/Single-page_application) 来处理来自专用 API 的数据。

# 要求

你有[节点。待安装的 JS](https://nodejs.org) (9.x 或更高版本)和 [NPM](https://www.npmjs.com) (通常一起安装)。

[下载并安装。](https://nodejs.org/en/download/)

建议使用最新版本(v10.9.0)。

也可以用[纱](https://yarnpkg.com)代替 [NPM](https://www.npmjs.com) 。

**齿轮。JS** 运行于:

*   苹果个人计算机
*   Linux 操作系统
*   Windows 操作系统

你可能想用牛逼的 [VSCode](https://code.visualstudio.com) 编辑器。

# 安装

[https://www.youtube.com/embed/UwWsSnFS0g0](https://www.youtube.com/embed/UwWsSnFS0g0)

# 用法

转到托管所有本地站点的目录。

```
$ cd ~/Sites 
```

通过命令创建新站点:

```
$ cogear new site.io -y # where "site" is your site folder name 
```

[https://www.youtube.com/embed/fweOYQ-_FU0](https://www.youtube.com/embed/fweOYQ-_FU0)

之后，转到网站目录:

```
$ cd ~/Sites/site.io 
```

并且启动了**齿轮。JS** 处于`development`或`production`模式([了解更多](https://cogearjs.org/docs/workflow#modes))。

```
$ cogear # run in develompent mode with hot-reload – by default

$ cogear production # build a site and run local server 
```

下次我们将深入研究工作流程。

Github 库:

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[codemotion](https://github.com/codemotion)/[cogear . js](https://github.com/codemotion/cogear.js)

### 现代静态网站生成器(Node.JS/Webpack)

<article class="markdown-body entry-content" itemprop="text">

# 齿轮。JS-现代静态网站生成器

[需求](https://raw.githubusercontent.com/codemotion/cogear.js/master/#requirements) | [安装](https://raw.githubusercontent.com/codemotion/cogear.js/master/#installation) | [使用](https://raw.githubusercontent.com/codemotion/cogear.js/master/#usage) | [选项](https://raw.githubusercontent.com/codemotion/cogear.js/master/#options) | [网站](https://cogearjs.org)

# 关于

**齿轮。JS** 是用[节点构建的静态站点生成器。JS](https://nodejs.org) 并基于 [Webpack](https://webpack.js.org) (v4.6)。

它受到 Jekyll 和其他人的启发，但建立在最新的前端技术之上。

提供出色的动态热重装体验，可即时实施任何更改。

# 介绍视频

[![Introduction to Cogear.JS](img/acee6f42d9ce86c915917cff17931f2e.png)T2】](https://youtu.be/nDga67P6lag?list=PLBuIATAeU0NWhrpWnI--TRG76DwAgem1I "Introduction to Cogear.JS")

## 特征

*   <g-emoji class="g-emoji" alias="desktop_computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f5a5.png">🖥</g-emoji> **现代堆栈技术**用捆绑的脚本和样式构建现代静态网站。快速制作原型并立即部署到服务器上。使用**任何现代前端栈** ( **webpack** 捆绑)–[Vue。JS](https://vuejs.org) 、[反应](https://reactjs.org)、[角度](https://angular.io)、[余光](https://emberjs.org)等。
*   <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji> **超快可靠**每秒执行近 1.000 页(取决于页面内容和原始计算机处理器能力)。在线。服务器每秒钟可以处理数千个请求来服务静态文件(甚至在微型 VPS 上)。
*   <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji> **对于任何主机**都不需要**任何数据库**(数据存储在平面…

</article>

[View on GitHub](https://github.com/codemotion/cogear.js)

官方网站:
[https://cogearjs.org](https://cogearjs.org)

文件:
[https://cogearjs.org/docs/](https://cogearjs.org/docs/)***