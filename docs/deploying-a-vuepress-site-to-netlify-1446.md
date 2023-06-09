# 将 vexpress 站点部署到 netlify

> 原文：<https://dev.to/raymondcamden/deploying-a-vuepress-site-to-netlify-1446>

在我开始之前，让我声明一下，我今天要讲的内容已经在文档([deployments-net lify](https://vuepress.vuejs.org/guide/deploy.html#netlify))中介绍过了，但是对我来说还不够详细，我想自己浏览一下，然后记录下这个过程。我不知道这是否有帮助，一如既往，我希望我的读者会告诉我，但我想我会分享它是如何为我工作的。还要注意的是，VuePress 仍处于早期开发阶段，所以我今天描述的内容在飞行汽车和喷气背包的遥远未来可能没有意义。

首先，为什么要这样做？正如我的老读者所知，我在 raymondcamden.com 使用它们已经有一段时间了。毫无疑问，他们是静态网站托管服务的*黄金标准*。是的，你可以使用亚马逊 S3，或者 [Surge](http://surge.sh/) (一项我也喜欢的服务，用于快速演示)，但是就你得到的所有附加功能而言，没有什么比 Netlify 更好的了。句号。

也就是说——我将要演示的特性是他们为与 GitHub 相关的站点自动构建的过程。我自己的博客就是这样运作的。我向我的存储库提交一个新帖子，Netlify 收到一个 ping，然后它开始使用 Jekyll 进行构建。那么这对于 VuePress 来说看起来怎么样呢？

让我们从一个不可思议的简单 VuePress 网站开始。VuePress 对你的默认结构没有任何要求，所以为了简单起见，我建立了一个只有两页的网站。我想澄清的是，这是对 VuePress 所有酷东西的一个很好的代表。我只是想为这个演示的目的一个“最低限度”的网站。

第一页是`index.md` :

```
### Hello

[Alpha](./alpha.html) 
```

第二页是`alpha.md` :

```
### Alpha

This is Alpha! Go [home](./). 
```

仅此而已。只是一个两页的静态网站。那我们怎么把这个交给 Netlify 呢？

### 第一步——GitHub

我做的第一件事是为网站创建一个 GitHub repo:[https://github.com/cfjedimaster/netlify-vuepress-demo](https://github.com/cfjedimaster/netlify-vuepress-demo)。很好，很简单，没什么大不了的。

### 第二步-无效站点

下一步-创建一个新的网站。您可以通过 CLI 实现这一点，但从 UI 也非常容易:

[![Netlify new site](img/670771cae41705d9d0fef9bd621a73e2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yW-z44MB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/vpn1.jpg)

选择您的存储库，在下一页中，您需要提供构建设置。请注意，这是*而不是*将立即工作，但我们会解决这个问题。

对于 build 命令，您希望使用`npm run docs:build`。

对于发布目录，`.vuepress/dist`。VuePress 文档假设有一个`docs`子目录，但是我们的应用程序的根是根文件夹本身。

[![Build settings](img/f60d74245bb163a2d2d284d7824be178.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---NjojJ6x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/vpn2.jpg)

继续点击`Deploy Site`，但是如我所说，预计它会失败。

### 第三步——做好网络准备

为了让网站正常工作，我们需要做一些事情。首先，我们必须告诉 Netlify 在构建过程中安装 vuepress。为此，创建一个 package.json:

```
npm init --force 
```

你不用用`--force`当然，我用那个是为了偷懒。在 package.json 中，然后添加一个新脚本。以下是我的完整文件:

```
{
  "name": "test1",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "docs:build": "vuepress build"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "vuepress": "^0.8.4"
  }
} 
```

老实说，我仍然是使用 npm 脚本的新手。我真的很喜欢他们，但是正如我所说的，我对他们来说是陌生的。将此文件添加到您的 repo 中，提交，就这样。说真的。这是我第一次测试的构建历史。在我找出 package.json 位之前，您可以看到它失败了。

[![Build log](img/83a7c4df40f0112b8300339cf8ea523d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pmwxJRT1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2018/05/vpn3.jpg)

你可以看到这个现场直播的网站，我的意思是，如果你真的想，这里:[https://tender-stonebraker-c8e749.netlify.com/](https://tender-stonebraker-c8e749.netlify.com/)。酷的部分是-我编辑我的 VuePress 网站，并确认它在本地很酷，然后我可以简单地提交我的更改。然后，Netlify 将获取更改，运行构建，并部署静态站点。

正如我所说，我希望这是有帮助的，如果你有任何问题，让我在下面的评论中知道！