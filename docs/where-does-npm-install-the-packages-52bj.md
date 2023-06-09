# npm 在哪里安装软件包？

> 原文：<https://dev.to/flaviocopes/where-does-npm-install-the-packages-52bj>

> 阅读 [npm 指南](https://flaviocopes.com/npm/)如果你刚开始使用 npm，它将包含许多基本的细节。

当您使用`npm`(或[纱线](https://flaviocopes.com/yarn/)安装套件时，您可以执行两种类型的安装:

*   本地安装
*   全局安装

默认情况下，当您键入一个`npm install`命令时，比如:

```
npm install lodash 
```

该包安装在当前文件树的`node_modules`子文件夹下。

此时，`npm`也在当前文件夹中的 [`package.json`文件](https://flaviocopes.com/package-json/)的`dependencies`属性中添加了`lodash`条目。

使用`-g`标志执行全局安装:

```
npm install -g lodash 
```

发生这种情况时，npm 不会在本地文件夹下安装软件包，而是使用全局位置。

具体在哪里？

`npm root -g`命令会告诉你在你机器上的确切位置。

在 macOS 或 Linux 上，这个位置可能是`/usr/local/lib/node_modules`。在 Windows 上可能是`C:\Users\YOU\AppData\Roaming\npm\node_modules`

但是，如果您使用`nvm`来管理 Node.js 版本，那么位置会有所不同。

例如，我使用了`nvm`，我的包位置显示为`/Users/flavio/.nvm/versions/node/v8.9.0/lib/node_modules`。