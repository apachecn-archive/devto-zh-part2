# 平面 node_modules 不是唯一的方式

> 原文：<https://dev.to/zkochan/flat-nodemodules-is-not-the-only-way-mo2>

**本文介绍了 pnpm 的一个旧版本。要获得文章[的更新版本，请点击这里](https://pnpm.js.org/blog/2020/05/27/flat-node-modules-is-not-the-only-way)。**

pnpm 的新用户经常问我关于 pnpm 创建的`node_modules`的奇怪结构。为什么不是平的？所有的子依赖项在哪里？

> 我假设这篇文章的读者已经熟悉了 npm 和 Yarn 创建的 flat `node_modules`。如果你不明白为什么 npm 3 不得不在 v3 中开始使用平`node_modules`，你可以在[中找到一些史前史为什么我们要使用 pnpm？](https://www.kochan.io/nodejs/why-should-we-use-pnpm.html)。

那么为什么 pnpm 的`node_modules`不寻常呢？让我们创建两个目录，在其中一个运行`npm install express`,在另一个运行`pnpm install express`。下面是你在第一个目录的`node_modules`中得到的顶部内容:

[![](../Images/19098b02e0d9daca3871398543e28b95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ECmw48Gt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4iw9vko9g7l5mhcwrfj1.png)

这里可以看到整个目录[。](https://github.com/zkochan/comparing-node-modules/tree/master/npm-example/node_modules)

这是您在 pnpm 创建的`node_modules`中得到的内容:

[![pnpm node_modules](../Images/fd78d0153e327765e0fd51965cf8bef0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wCQIpD-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3n9yg9ead1tfcfxugrgd.png)

你可以在这里查看[。](https://github.com/zkochan/comparing-node-modules/tree/master/pnpm-example/node_modules)

那么所有的依赖在哪里呢？在`node_modules`中只有一个名为`.registry.npmjs.org`的文件夹和一个名为`express`的符号链接。嗯，我们只安装了`express`，所以这是您的应用程序必须访问的唯一包

> 阅读更多关于为什么 pnpm 的严格是一件好事[在这里](https://medium.com/pnpm/pnpms-strictness-helps-to-avoid-silly-bugs-9a15fb306308)

让我们看看里面有什么:

[![](../Images/73cd9f2d7af93964258e2d92489e06f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8bKD1bD6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8pf5cwjo8ndr7xzxu0x7.png)

`express`没有`node_modules`？`express`的属地都在哪里？

诀窍在于`express`只是一个符号链接。当 Node.js 解析依赖关系时，它使用它们的真实位置，所以它不保留符号链接。但是你可能会问，`express`的真实位置在哪里？

这里:[node _ modules/. registry . npmjs . org/express/4 . 16 . 3/node _ modules/express](https://github.com/zkochan/comparing-node-modules/tree/master/pnpm-example/node_modules/.registry.npmjs.org/express/4.16.3/node_modules/express)。

好了，现在我们知道了`.registry.npmjs.org/`文件夹的用途。`.registry.npmjs.org/`将所有的包存储在一个平面文件夹结构中，所以每个包都可以在一个文件夹中找到，这个文件夹的名称是:

```
.registry.npmjs.org/<name>/<version>/node_modules/<name> 
```

Enter fullscreen mode Exit fullscreen mode

这种扁平结构避免了由 npm v2 创建的嵌套`node_modules`导致的长路径问题，但与 npm v3，4，5，6 创建的扁平`node_modules`不同，它保持了包的隔离。

现在让我们来看看`express`的真实位置:

[![](../Images/dff27b33ae83ba120f1f85c2a7c20884.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HedDouxV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/53zr83hig44wti1zpwy8.png)

是骗局吗？它还缺`node_modules`！pnpm 的`node_modules`结构的第二个技巧是包的依赖关系与依赖包的实际位置在同一个目录级别上。所以`express`的依赖关系不在`/express/4.16.4/node_modules/express/node_modules/`而是在[/express/4 . 16 . 4/node _ modules/](https://github.com/zkochan/comparing-node-modules/tree/master/pnpm-example/node_modules/.registry.npmjs.org/express/4.16.3/node_modules):

[![](../Images/31795a52eb81e0ee5ca5d21faa3c43e0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--683WOr2---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/09a98isedcqvhciwk2z7.png)

`express`的所有依赖项都是指向`node_modules/.registry.npmjs.org/`中适当目录的符号链接。将`express`的依赖关系提高一级可以避免循环符号链接。

正如你所看到的，尽管 pnpm 的`node_modules`结构初看起来不寻常

1.  它完全兼容 Node.js
2.  包和它们的依赖项被很好地分组

对于具有对等依赖关系的包来说，结构稍微复杂一些，但是想法是一样的:使用符号链接创建一个具有平面目录结构的嵌套。

* * *

如果你想尝试一下[的 pnpm](https://github.com/pnpm/pnpm/) ，你可以很容易地用 npm: `npm i -g pnpm`安装它。然后在需要装东西的时候运行它而不是 npm 就可以了:`pnpm install foo bar`。