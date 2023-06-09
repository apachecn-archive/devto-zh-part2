# 节点程序包管理器命令

> 原文：<https://dev.to/mattgredler/node-package-manager-commands-3c38>

Node 是一个 JavaScript 运行时环境，作为一个轻量级但健壮的服务器。Node 于 2009 年 5 月 27 日发布，它似乎不会去任何地方。它被一些科技界的大腕所使用，包括；贝宝、网飞和优步。节点的 JavaScript 在您的浏览器中执行。对于 Chrome 用户来说，这意味着它是由其引擎 V8 运行的。

节点包管理器在发布后不久就实现了，增加了软件的吸引力。目前，核心功能最流行的 NPM 软件包是 express。Express 是一个框架，允许开发人员快速、轻松地部署应用程序。它通过实施路由、标准性能修复和内容协商来实现这一点。

软件包的安装发生在控制台中。NPM 的基本命令是:

```
//This command would install express.
npm install express

//The aforementioned command can shortened by typing i instead of install.
npm i express. 
```

安装软件包后，系统会要求您为该应用程序分配几个值，例如应用程序的名称。你可以跳过这一步，像这样接受所有的标准答案。

```
npm i express -y 
```

如果你对你的主机有任何经验，这些命令应该感觉相当熟悉。这里的是一些帮助你入门的命令。