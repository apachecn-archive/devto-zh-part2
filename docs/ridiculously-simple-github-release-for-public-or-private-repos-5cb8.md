# 简单得可笑的 Github 公开或私下回购版本

> 原文：<https://dev.to/adhocore/ridiculously-simple-github-release-for-public-or-private-repos-5cb8>

[https://github.com/adhocore/please](https://github.com/adhocore/please)

Please 是一个 bash 脚本，用于在 Github 中自动发布新版本，支持公共和私有存储库。如果你接受 semver，这就是适合你的工具。如果您维护许多包和它们的版本/发布，这将节省您的时间并提高生产率。

## 它的作用和作用

*   根据本地 git 配置确定远程 git repo
*   检验和更新`master`
*   从 github api 端点获取最新版本
*   收集自最新发布以来的新提交
*   创建一个 changelog(如果存在的话，添加到前面)，将它反映到`./CHANGELOG.md`文件中并提交它
*   碰撞版本，将其反映到`./VERSION`文件中
    *   如果任何以`feat` `<minor>`开始的最新提交被取消
    *   但是如果你没有明确地传递`scope`就好了
*   如果有`package.json`文件，点击`package.json#version`
*   提交所有更改
*   如果`package.json#private`没有被设置为`true`
    *   如果 npm 用户尚未登录，运行`npm adduser`
    *   最后运行`npm publish`
*   发布新版本并标记到 github
*   如果有`box.json`文件，使用`box`编译 phar(如果需要，下载`box.phar`)
*   上传编译后的`phar`作为最近发布的资产

下面是它漂亮的用户界面的预览:

[![Help Preview](img/60579c80114b4e336c9b7d20110ea7ba.png "Help Preview")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1RImjYnF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/zxc5deM.png)