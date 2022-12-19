# 使用 npm 6+的新 npm 审计

> 原文：<https://dev.to/letsbsocial1/the-new-npm-audit-with-npm-6-2ipf>

[![Node-Security-Project.png](../Images/6bfb37906e5bd77580713818e8f6e8f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VKOh95sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilu9d3745hd418x1tnbo.jpg)

*本帖[首发](http://www.mariadcampbell.com/2018/06/05/the-new-npm-audit-with-npm-6/)于我的**开发者博客**，***2018 年 6 月 5 日*** 。*

首先 **Github** 开始让我们在 **Github** 回购中了解`npm package vulnerabilities`。现在`Nodejs`也跟着这么做了，并通过**命令行**在我们的**本地 repos** 中做了同样的事情。

我花了一点时间才想出如何修复这些漏洞。这是一个**不那么热**T0】的问题。好像后来有所改善！**节点安全**毕竟是 ***非常*** **新**！更好的文档链接现在包含在我们的**终端(Mac OSX)** 中的漏洞警告中。

目前我正在用`express`、`nodejs`、`sequelize`、`express-session`、`bcrypt`等做一个应用。我想包含`sequelize-cli`，并使用命令
来实现

```
npm i sequelize-cli --save 
```

Enter fullscreen mode Exit fullscreen mode

然而，安装后，我在**终端**得到如下警告:

```
sequelize-cli@4.0.0
added 53 packages from 34 contributors and audited 2069 packages in 10.745s
found 1 low severity vulnerability
run `npm audit fix` to fix them, or `npm audit` for details 
```

Enter fullscreen mode Exit fullscreen mode

首先我按照指示用
修复**漏洞**的

```
npm audit fix 
```

Enter fullscreen mode Exit fullscreen mode

这不起作用。我得到了警告

```
up to date in 2.155s
fixed 0 of 1 vulnerability in 2069 scanned packages
1 vulnerability required manual review and could not be updated 
```

Enter fullscreen mode Exit fullscreen mode

然后我跑了

```
npm audit 
```

Enter fullscreen mode Exit fullscreen mode

信息包括到**节点安全**的链接，以及要采取的后续步骤:

```
npm audit                                                                                      ✖ ✹ ✭

                       === npm audit security report ===

┌──────────────────────────────────────────────────────────────────────────────┐
│                                Manual Review                                 │
│            Some vulnerabilities require your attention to resolve            │
│                                                                              │
│         Visit https://go.npm.me/audit-guide for additional guidance          │
└──────────────────────────────────────────────────────────────────────────────┘
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Low           │ Prototype Pollution                                          │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Package       │ deep-extend                                                  │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Patched in    │ &gt;=0.5.1                                                      │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Dependency of │ bcrypt                                                       │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Path          │ bcrypt &gt; node-pre-gyp &gt; rc &gt; deep-extend                     │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ More info     │ https://nodesecurity.io/advisories/612                       │
└───────────────┴──────────────────────────────────────────────────────────────┘ 
```

Enter fullscreen mode Exit fullscreen mode

它涉及到包`deep-extend`，它是`sequelize-cli`和`bcrypt`的依赖项，这两者我都包含在我的**根依赖项**中。我在**节点安全**链接中获得了以下关于`deep-extend`的信息:

```
Overview

Versions of deep-extend before 0.5.1 are vulnerable to prototype pollution.

Remediation

Update to version 0.5.1 or later. 
```

Enter fullscreen mode Exit fullscreen mode

当我在**终端**运行`npm audit`时，它告诉我进入位于`node_modules`的包并检查一个`package-lock.json`确实存在。如果没有，我应该创建一个:

```
 ✖ ✹ ✭
npm ERR! code EAUDITNOLOCK
npm ERR! audit Neither npm-shrinkwrap.json nor package-lock.json found: Cannot audit a project without a lockfile
npm ERR! audit Try creating one first with: npm i --package-lock-only

npm ERR! A complete log of this run can be found in:
npm ERR! /Users/mariacam/.npm/_logs/2018-06-05T10_22_24_882Z-debug.log 
```

Enter fullscreen mode Exit fullscreen mode

但是首先我去掉了我的**顶级** `package-lock.json`，这样我就可以升级`deep-extend`。如果我保存了它，`deep-extend`将会被重新安装相同的版本。要了解更多信息，请访问[npmjs.com](https://docs.npmjs.com/files/package-lock.json)上的 [package-lock.json](https://docs.npmjs.com/files/package-lock.json) 。

在我**删除了**顶层** `package-lock.json`的**之后，我又进入了`node_modules`中的`sequelize-cli`，里面包含了`deep-extend`的依赖，看到没有`package-lock.json`。我运行以下命令为`sequelize-cli` :
创建一个

```
npm i --package-lock-only 
```

Enter fullscreen mode Exit fullscreen mode

运行之后，我在**终端**得到如下警告:

```
created a lockfile as package-lock.json. You should commit this file.
added 839 packages from 79 contributors and audited 4797 packages in 17.936s
found 18 vulnerabilities (3 low, 9 moderate, 5 high, 1 critical)
run `npm audit fix` to fix them, or `npm audit` for details 
```

Enter fullscreen mode Exit fullscreen mode

我返回到根目录并运行下面的命令:

```
npm i deep-extend@0.5.1 
```

Enter fullscreen mode Exit fullscreen mode

同样，我在终端中得到以下警告:

```
deep-extend@0.5.1
added 1 package from 5 contributors, updated 1 package and audited 2070 packages in 3.454s
found 1 low severity vulnerability
run `npm audit fix` to fix them, or `npm audit` for details 
```

Enter fullscreen mode Exit fullscreen mode

这个**安装了****版本需要****去掉****漏洞**，如前所述。

现在我准备运行命令

```
npm audit fix 
```

Enter fullscreen mode Exit fullscreen mode

然后收到

```
audited 2070 packages in 3.049s
found 0 vulnerabilities 
```

Enter fullscreen mode Exit fullscreen mode

我也收到了关于 **npm 包** `sharp`的警告，并且卸载了它，现在，如果我真的想的话，我可以 ***重新安装******修复*** 漏洞**。这也适用于**任何漏洞**你可能必须**修复**你 **Github** 上的**！我知道我有几个问题要解决！****

[![Node Security Project](../Images/1bfcb372b0661b522b631da3c44209f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3UvprVxv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cs07rqxkdwx5d28lcavx.png)**