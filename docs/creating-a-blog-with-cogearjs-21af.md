# 用 Cogear 建立一个博客。射流研究…

> 原文：<https://dev.to/cogear/creating-a-blog-with-cogearjs-21af>

最近在对 [Cogear 的评论中。有人问我它和](https://dev.to/codemotion/cogearjs--modern-static-websites-generator-450a)[盖茨比](https://gatsbyjs.org)的区别。

今天我读了这篇文章[用 Gatsby . js&React](https://dev.to/saigowthamr/build-a-blog-using-gatsbyjs--react--2lo)at**dev . to**。我想通过做同样的任务来比较它们。

让我们用 Cogear 建立一个博客。JS 。

首先你可以看看真实的工作例子:
[https://cogearjs.org/blog](https://cogearjs.org/blog)

# 安装

确保[符合](https://cogearjs.org/docs#requirements)的要求。

安装**齿轮。JS** 做下一步:

```
$ npm install -g cogear
# or
$ yarn global add cogear 
```

[https://www.youtube.com/embed/UwWsSnFS0g0](https://www.youtube.com/embed/UwWsSnFS0g0)

# 博客神石

我们需要转换成博客默认网站。

这需要两件事:

1.  [博客插件](https://github.com/codemotion/cogear-plugin-blog)。
2.  [博客主题](https://github.com/codemotion/cogear-theme-blog)。

有一种通过内置发电机的标准方式:

```
$ cd ~/Sites
$ cogear new blog 
```

[https://www.youtube.com/embed/fweOYQ-_FU0](https://www.youtube.com/embed/fweOYQ-_FU0)

但是我已经为你准备了一个预置，包括插件和主题。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png)[codemotion](https://github.com/codemotion)/[cogear-preset-blog](https://github.com/codemotion/cogear-preset-blog)

### Cogear 的博客预设。射流研究…

<article class="markdown-body entry-content" itemprop="text">

# 齿轮。JS 预置`blog`

准备使用站点预设。只需从命令行克隆存储库、安装依赖项并在项目目录中运行`cogear`。

[![screenshot](img/80adef1662f578f7e88136a298257d65.png)T2】](https://github.com/codemotion/cogear-theme-blog/raw/master/screenshot.jpg)

# 装置

确保[啮合。JS](https://cogearjs.org) 安装完毕。[如何安装](https://cogearjs.org/docs/)

将此存储库克隆到目标本地目录:

```
> cd ~/Sites/
> git clone --recurse-submodules https://github.com/codemotion/cogear-preset-blog blog
```

注意`--recurse-submodules`选项。之所以需要，是因为 [`cogear-theme-blog`](https://github.com/codemotion/cogear-theme-blog) 呈现为 git [子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)。

安装依赖项:

```
> cd ~/Sites/blog
> npm install
or
> yarn install
```

你已经准备好了！点燃**齿轮。JS** 在`dev`模式下，你的新博客会自动在浏览器中打开！

```
> cogear # development mode by default
```

# 阅读文件

[https://cogearjs.org/docs](https://cogearjs.org/docs)

</article>

[View on GitHub](https://github.com/codemotion/cogear-preset-blog)

让我们用预设安装博客。

```
$ git clone --recurse-submodules https://github.com/codemotion/cogear-preset-blog blog 
```

现在您需要安装依赖项:

```
$ cd ~/Sites/blog
$ npm install
# of
$ yarn install 
```

# 搞定

真的，这就是你要做的。

现在启动齿轮。JS 处于开发模式。

```
$ cogear 
```

你的博客准备好了:
[![Cogear.JS blog](img/80adef1662f578f7e88136a298257d65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OHfVvSnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/codemotion/cogear-theme-blog/raw/master/screenshot.jpg)

我已经把结果上传到:
[https://cogear-blog . now . sh](https://cogear-blog.now.sh)

看看这个。

是时候创建第一个帖子了。

创建包含以下内容的`./src/pages/p/first-post.md`文件:

```
---
title: "First  post"
tags:
  - news
---
# This is my first post! 
```

干得好。保存它，浏览器页面将自动重新加载。

[![Cogear.JS blog with post](img/218ef06c6147302ef6ee8e352f8b341c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qlHXoyJy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cg6z5a9fzdqqerakxowh.png)

结果也可以通过链接得到:
[https://cogear-blog-with-post . now . sh](https://cogear-blog-with-post.now.sh)

为什么博客选择了`p`文件夹？

定制这种行为很容易。

从此处编辑`./config.yaml`文件

```
title: Blog | Cogear.JS – modern static websites generator
description:
keywords:
theme: blog
blog:
  index: ""
  regex: "^p/"
  tagUri: 'tag'
  perPage: 5
pages:
  ^p/:
    layout: post
    author:
      login: Dmitriy Beliaev
      avatar: 'https://www.gravatar.com/avatar/400caf343d3bab57ab93f63e21a12be7?s=24'
      link: https://cogearjs.org 
```

对此

```
title: Blog | Cogear.JS – modern static websites generator
description:
keywords:
theme: blog
blog:
  index: ""
  regex: "^blog/"
  tagUri: 'tag'
  perPage: 5
pages:
  ^blog/:
    layout: post
    author:
      login: Dmitriy Beliaev
      avatar: 'https://www.gravatar.com/avatar/400caf343d3bab57ab93f63e21a12be7?s=24'
      link: https://cogearjs.org 
```

并将文件夹`./src/pages/p`重命名为`./src/pages/blog`。

更多关于配置文件的视频在下面:
[https://www.youtube.com/embed/hEUgZcoFKWM](https://www.youtube.com/embed/hEUgZcoFKWM)

欢迎您提问！