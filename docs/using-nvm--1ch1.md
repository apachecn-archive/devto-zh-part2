# 使用 nvm

> 原文：<https://dev.to/dhruv/using-nvm--1ch1>

如果您同时从事一个以上的项目，或者您做了大量的开源工作，或者您决定回到一年前的项目，那么很有可能您将不得不更改 node.js 版本。可能有一些依赖项只适用于特定的 node.js 版本。您可以使用 nvm 立即更改 node.js 版本。

如果您还没有安装 nvm。下载并安装最新版本的 nvm。
`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash`

通过
`nvm – version`检查您的 nvm 版本

要下载并安装 node.js 的新版本，运行
`nvm install 8.2`
用你需要的版本号替换 8.2。

运行
`nvm ls`
来检查你的系统上安装了什么版本的 node.js，这会列出你已经安装的所有版本。

要切换到任何 node.js 版本，您需要运行
`nvm use 8.2`

如果您不想要任何特定的 node.js 版本，您可以通过
`nvm uninstall 8.2`卸载它

您可以通过设置别名
`nvm alias default v9.3.0`来设置默认的 node.js 版本

这篇文章最初发表在[媒体](https://medium.com/@nendhruv/using-nvm-bd11f396902d)上