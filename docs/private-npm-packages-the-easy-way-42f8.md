# 二等兵 NPM 用简单的方式打包

> 原文：<https://dev.to/thebenforce/private-npm-packages-the-easy-way-42f8>

假设您刚刚编写了一个令人惊叹的节点模块，您希望将其分解成一个包，以便可以重用。问题是，它包含一些超级机密的公司资料，不能共享，你不能，或者不想，创建一个私人的 npm 组织。那你现在做什么？

信不信由你，如果你正在使用私有的 git 库，那么你已经拥有了你需要的一切。

## 从 git 安装软件包

用 git 安装包其实真的很简单，如果你用 github，gitlab，bitbucket。从 git 安装只需要一个特殊的包名。格式为`SERVICE:user/repo`。

因此，如果你的用户名是`stan`并且你试图从 gitlab 安装`willzyx` repo，那么执行下面的命令:

```
npm install gitlab:stan/willzyx 
```

Enter fullscreen mode Exit fullscreen mode

## 版本怎么样？

要安装特定的标签或分支，请在包名的末尾添加`#NAME`。如果您使用 npm 版本来更新您的包版本，那么您可以添加`#semver:^1.0`来安装 1.x 的最新版本。

## 复杂的包呢？

当然，这对于普通的 javascript 包来说很好，但是如果您使用的是 Typescript 之类的高级东西呢？如何在不将构建工件签入到您的 repo 中的情况下构建包呢？

`package.json`的`scripts`部分有一些特殊的脚本，作为特定 npm 命令的一部分运行。你要定义的是`prepare`。该脚本在软件包安装后运行。

为了继续 typescript 示例，您可以使用下面的准备脚本:

```
"prepare": "node_modules/.bin/tsc" 
```

Enter fullscreen mode Exit fullscreen mode

任何开发依赖项将在脚本运行前安装，并在运行后清理。

## 现在已经建好了，我怎么清理？

如果你使用 git，这个也很简单。就像 git 有`.gitignore`文件一样，npm 有一个`.npmignore`文件。`.npmignore`具有相同的格式，在准备脚本完成后应用。这意味着您可以将源目录添加到`.npmignore`中，在您安装完软件包后，它会神奇地消失。

* * *

希望这篇文章已经鼓励您创建一些自己的包。如果你有任何我可能遗漏的其他提示，请在评论或 twitter 上与我分享。

* * *

## 参考文献

*   [安装| npm 文档](https://docs.npmjs.com/cli/install)
*   [脚本| npm 文档](https://docs.npmjs.com/misc/scripts)