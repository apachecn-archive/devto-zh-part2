# NextJS 技巧:动态服务器的热重装

> 原文：<https://dev.to/whoisryosuke/nextjs-tip-hot-reloading-for-dynamic-servers-43ed>

你是否一直在开发一个带有动态路由的 [NextJS](http://nextjs.org) 应用程序(使用 maybe Express)，并且发现每次你做一个改变都要做关闭服务器(CTRL+C)并重新启动的繁琐过程？(`npm run dev`)。

如果你习惯于和 [NodeJS](http://nodejs.org) 或者 [ExpressJS](https://expressjs.com/) 一起工作，你可能会遇到 [nodemon](https://github.com/remy/nodemon) 。这是一个在基于节点的服务器上启用热重装的实用程序，因此无论何时您对服务器文件进行更改并保存，它都会立即重新启动，而无需您的任何提示。

但是 **nodemon 不能与 NextJS** 一起开箱即用，并且需要*少量的*配置。如果你尝试在没有配置或正确的 CLI 参数的情况下运行 nodemon，你会发现你的服务器将开始运行*真正不可靠的*。我的服务器开始无限重启，因为它在每次 NextJS 编译时都会检测到变化，从而触发编译的无限循环。

> 本指南假设您有一个带有动态路由设置的 NextJS 项目。你可以在 NextJS repo 的[示例部分找到一些](https://github.com/zeit/next.js/tree/master/examples)

## 解？

Nodemon 接受一个配置文件，这允许您对过程进行更大程度的控制。通过向该文件添加一些值，我们可以解决所有问题。

### 安装 nodemon

如果您还没有安装 nodemon:

`npm install --save-dev nodemon`

### 创建配置文件

在项目根目录下创建一个`nodemon.json`文件，并将以下内容粘贴到其中:

```
{  "verbose":  true,  "ignore":  ["node_modules",  ".next"],  "watch":  ["server/**/*",  "server.js"],  "ext":  "js json"  } 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 nodemon 忽略`.next`文件夹，它被用作下一个编译器的缓存(并触发无限重载)。我们还告诉它从哪个文件开始观察变化。我把我的服务器文件保存在一个单独的服务器文件夹中，因为我有像路由/中间件等需要单独文件和文件夹的东西。

### 更新您的 npm 开发脚本

现在您可以修改您的`package.json`并更新‘dev’脚本值以使用 nodemon 而不是默认的`node server.js` :

```
 "scripts": {
    "dev": "nodemon -w server/server.js server/server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以运行`npm run dev`了，您将拥有一个热重装服务器。

我在[next js Github 问题](https://github.com/zeit/next.js/issues/791)上找到了这个解决方案，在那里，一个人也遇到了同样的问题。

希望这对✌️有所帮助

* * *

**参考文献**:

*   [节点门](https://github.com/remy/nodemon)
*   [NextJS Github 问题-热重装](https://github.com/zeit/next.js/issues/791)