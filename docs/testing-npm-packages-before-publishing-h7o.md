# 发布前测试 npm 包

> 原文：<https://dev.to/vcarl/testing-npm-packages-before-publishing-h7o>

Erol Ahmed 在 Unsplash 上拍摄的照片

当开发一个 npm 包时，你必须确认它确实可以被使用。如果测试通过了，示例构建了，演示运行了，那就太好了，但是如果消费者不能安装它，那就坏了。npm 提供了一个工具来帮助在发布之前测试包。[医生解释得很好](https://docs.npmjs.com/cli/link)，但下面有个小例子。您可以在您的包目录中运行一次，然后在您的应用程序目录中运行一次。

```
~/workspace/package-name $ npm link
~/workspace/some-application $ npm link package-name 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的选择——当它起作用的时候。`npm link`早在`npm@1.0`之前就已经存在了，在构建步骤成为标准，浏览器 JS 成为 npm 的一部分之前，这些工具的采用带来了一些问题。我遇到了三个主要问题。

## 有些构建工具不理解符号链接。

成熟的构建工具通常没有这个问题，或者会发现符号链接的问题并迅速解决。然而，这是新发布工具的常见问题，也是更常见的回归之一。由于这些问题，对于如何解决符号链接，webpack 有特殊的配置。该文档特别提到了 npm 链接的潜在问题。

## 符号链接并不验证你是否正确打包了它。

即使你已经让你的符号链接包正确运行，它也不会告诉你一旦你发布它它是否会工作。您只知道磁盘上的所有文件都将运行。已经发布到 npm 的包被打包成一个 tar 归档文件，并且有一些关于如何打包的配置。符号链接不经过这个过程，留给你的是未经测试的配置。

## 模块解析不适用于 2 个文件树。

因为这个包是一个符号链接，所以它存在于不同的文件树中。这在模块解析中造成了一个死角——破坏了 React 包。当您的包加载中的组件做出反应时，它会在文件树中查找一个`node_modules/react`文件夹，并且只找到它自己的——而不是您的应用程序加载的那个。这会导致加载两个不同的 React 副本，从而导致各种问题。

这是最糟糕的问题。工具偶尔坏掉是一回事，但是对于整个类别的库来说，`npm link`从根本上来说是不兼容的。这些问题让我根本不想使用`npm link`。我浪费了大量的时间试图为我正在开发的包争论符号链接，但从来没有让它达到可靠的程度。

现在，我使用 npm 包。

# npm 包

[`pack`命令](https://docs.npmjs.com/cli/pack)创建一个. tgz 文件，就像你要把包发布到 npm 一样。它从 package.json 中提取名称和版本，生成类似于`package-name-0.0.0.tgz`的文件。这可以复制、上传或发送给同事。如果您发布该文件，它将被上传到 npm。

```
~/workspace/package-name $ npm pack 
```

Enter fullscreen mode Exit fullscreen mode

一旦有了文件，就可以安装了。除了 npm，我还可以从更多的来源安装，我强烈建议[浏览文档](https://docs.npmjs.com/cli/install)。我们必须指定文件的完整路径，所以为了方便起见，我通常先把它复制到我的主目录。

```
~/workspace/package-name $ npm pack
~/workspace/package-name $ cp package-name-0.0.0.tgz ~
~/workspace/some-application $ npm install ~/package-name-0.0.0.tgz 
```

Enter fullscreen mode Exit fullscreen mode

您也许可以在您的终端中设置一个别名或函数来实现这一点，但是我并不经常这么做。`npm pack | tail -n 1`将只输出文件名到标准输出。(写完这篇文章后，我了解到 [`yalc`](https://github.com/whitecolor/yalc) 是一个自动完成这项工作的工具)

这贯穿了一个完整的发布周期——它甚至运行了`publish` npm 脚本以及相关的前期和后期脚本。打包并安装它是模拟发布一个包的一种极好的方式，它避免了符号链接的所有怪癖和问题。

我知道当我第一次尝试向 npm 发布包时，我面临的一个障碍是弄清楚它是否真的能工作。出版感觉如此最终；你把它公布于众，这个版本号就再也不能使用了。帮助我更加自信，它会按照我期望的方式运行。

* * *

感谢阅读！我在推特上的名字是 [@vcarl_](https://twitter.com/vcarl_) 。我主持了 React 开发者聊天室[React flux](http://join.reactiflux.com/)和 Node 聊天室 [Nodeiflux](https://discordapp.com/invite/vUsrbjd) 。JS 开发者。如果您有任何问题或建议，请联系我们！