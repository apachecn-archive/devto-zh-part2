# 步步 React，NodejS 和 MySQL 简单全栈应用 2018(第三部分)

> 原文：<https://dev.to/kmaryam27/step-by-step-react-nodejs-and-mysql-simple-full-stack-application-2018-1n70>

#### 在这篇文章中，我描述了用 javaScript 语言开发简单的访问数据库的后端并显示表格的信息

在安装了来自:[https://nodejs.org/en/download/](https://nodejs.org/en/download/)的 nodeJS 和来自:【https://code.visualstudio.com/download】T2 的 visual studio 代码以及来自:[https://git-scm.com/downloads](https://git-scm.com/downloads)的 GitBash 之后，我们可以开始开发项目的后端，但是在开始之前，我们需要一些关于 nodeJs 的知识。

#### * node . js 到底是什么？

Node.js 是一个 JavaScript 运行时环境。这是一个基于 Chrome 的 JavaScript 运行时的平台，用于轻松构建快速和可扩展的网络应用程序。Node.js 使用事件驱动的非阻塞 I/O 模型，这使得它轻量级且高效，非常适合跨分布式设备运行的数据密集型实时应用程序。

#### *什么是 Git Bash？

Git bash 是包含在“Git for Windows”中的一个 msys shell，是 Cygwin 的一个精简版本(一个旧版本)，它的唯一目的是提供足够的 POSIX 层来运行 bash。提醒:msysGit 是为 Windows 编译 Git 的开发环境。

1-打开 git bash 并在您选择的后端地址创建一个文件夹示例您可以使用#mkdir 命令在 Git Bash 中创建一个新文件夹，并使用#code 在 VScode 环境中打开该文件夹..命令

[![](img/b68a658b7dfcc883157d7b4d60cd2705.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hMx3n6V2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zkolycx57vforwc1h43p.png)

2-添加新的。js 文件添加到文件夹中，并将其命名为 server.js。此文件是项目的服务器，请将以下代码添加到 server.js 文件中:

[![](img/a1f46e72b48d80f5dd4b3fe9bb93c8eb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--c0Kyo-ld--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4v70nqzd4ftkvkh2wvg.png)

我试图在图片的评论中简单解释一下 server.js 中的每一行代码，但是更多的信息你可以通过这个链接观看视频:[https://expressjs.com/](https://expressjs.com/)

3-右键单击 server.js 并选择在终端中打开选项，在终端中使用 npm init 命令并回答关于新文件的问题(只需输入即可回答)创建一个新文件(package.json)

[![](img/9693fb5a9eb9e79f89cb3e64c2e1e188.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GmRYlEuL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jysinkq5mn5mybiyjb0k.png)

现在使用 npm install express 命令在您的项目上安装 express 模块，以便可以运行它。最后用 node server 或 node server.js 命令运行你的项目

[![](img/be7b78f0dae28bbc5bc095393753a421.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Up7wsQIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/begmxr04czwydxshkg51.png)
[![](img/cc6897e7de24866207f2de1388a16fa4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lmvXO3vF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7y0rl2d1si2dqbac1j54.png)
[![](img/5b41747e08b2aa6f5476f065162578fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7IVTpBx6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h6157julktbp9enpr6r0.png)

5-在这一步中应该制定一条路线来解决我们项目上的无法得到/错误。所以我们应该创建一个新的文件夹(routes)并添加一个新的。js 文件(html-routes.js)添加到它。编写完 html-routes.js 并运行 project 之后，您应该会在 localhost:3000 address 的浏览器上看到来自 simple-react project 的 Hello

[![](img/37f2b14ada3bc058afa5e212825959cd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0mWKexEa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c7y7m58pn4m7bl63zc9u.png)

6-现在我们必须连接到 MySQL 数据库。因此，我们应该在 server.js 文件上添加一些代码，以便在数据库和服务器之间建立连接:

[![](img/6a8b2524333b6233b3a5360105c2a98d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JtokPv3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8uzbn5j10jov2oye3d0h.png)

现在在终端使用 npm instal mysql 命令，我们应该将 mysql 模块添加到项目中。运行后，我们将在终端上看到下面的错误，我们必须做另一个步骤来修复它。这是 MySQL 版本 8 中的一个问题

[![](img/831e62157364c913ad4ac5d374d03b06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kPPVc0E4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/019fajxykfx4dp613wgl.png)

输入数据库的用户名和密码

[![](img/0b984302c0284cc8813222af71743669.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F2kSxZZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6z20gu2y3uw60tofsn9c.png)

最后:

[![](img/b1edb99d5af9745f6bb6dd9ca1cbc75b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YkQfFChW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/34kjkdxqgujkyspwzazt.png)
[![](img/12f9afffd905af8967e9ba69099ea273.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--YZXRUE0b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bi936iazo6drapn0c5g3.png)

资源:

[https://medium . freecodecamp . org/what-exact-is-node-js-ae36e 97449 F5](https://medium.freecodecamp.org/what-exactly-is-node-js-ae36e97449f5)
[https://www . tutorialspoint . com/nodejs/nodejs _ introduction . htm](https://www.tutorialspoint.com/nodejs/nodejs_introduction.htm)
[https://stack overflow . com/questions/17807485/what-is-the-exact-meaning-of-git-bash](https://stackoverflow.com/questions/17807485/what-is-the-exact-meaning-of-git-bash)