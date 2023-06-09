# 了解如何使用 Lepto 自动化您的图像优化过程

> 原文：<https://dev.to/dimitrinicolas/learn-how-to-automate-your-image-optimization-process-with-lepto-890>

优化图像是一项长期的任务，也许你已经在使用 imagemin 或 ImageOptim，它们是很好的工具，但似乎很难自动使用它们。

当你想优化你的网页图片时，你有很多任务要做，比如:

*   调整大小

*   创建不同分辨率的文件(用于 Retina 屏幕)

*   优化(删除元数据)

*   压缩

*   创造更好的格式选择(如 WebP)

我们已经通过模块捆绑器和前/后处理器编译了 JavaScript 和 CSS，下面是新的构建步骤:图像处理。

## 轻者入门

[github.com/leptojs/lepto](https://github.com/leptojs/lepto)

Lepto 是一个 Node.js 模块和命令行接口，负责**图片资源的编辑、优化和分析。所以让我们开始吧！**

首先，你将**需要有一个“npm 初始化项目”**。如果没有，运行命令`npm init`。

您还需要安装 CLI 工具作为开发依赖:

```
$ npm install -D lepto-cli 
```

Enter fullscreen mode Exit fullscreen mode

如果你**懒得收集一些普通的图片**，我给你一些[你可以根据这个要点](https://gist.github.com/dimitrinicolas/766eecec10adf4deb6a20c8002df4f2b)下载，只需点击“下载 ZIP”按钮，将这些资源放入“资产”目录。

然后，您必须创建名为`lepto.config.json` :
的配置文件

```
{  "input":  "assets",  "output":  "assets/output",  "watch":  true,  "filters":  [  {  "glob":  "**/*.*",  "use":  [  {  "name":  "lepto.jpeg",  "quality":  75  },  {  "name":  "lepto.png",  "quality":  75  }  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

在你的`package.json` :
中创建一个新的名为`build`的 npm 脚本

```
{  ...  "scripts":  {  "build":  "lepto",  ...  }  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过运行命令`npm run build`，Lepto 将从您的“assets”目录中压缩所有 jpg 和 png，并将它们导出到输出目录中。通过将`watch`选项设置为`true`，Lepto 将监视“资产”目录中的每一个变化，以重新启动该进程。

默认情况下，Lepto 是零配置的，所以你需要**写一个`filters`** 列表，其中每个都与一个`glob`和一个`use`插件列表相关联。在这个配置文件示例中，我们要求 Lepto 以 75 的质量压缩所有 jpg 和 png 文件。

[![lepto command running](img/4da95d958c9427729131491302b4278c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6waeHUlk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbnrqit1ip2feyxdfsxv.png)

## 让我们添加一些插件

现在，你想调整你的图像。有一个插件: [`lepto-resize`](https://github.com/leptojs/lepto-resize) ，你必须这样安装:

```
$ npm install -D lepto-resize 
```

Enter fullscreen mode Exit fullscreen mode

### 然后我们将在我们的 Lepto 配置中添加一个新的过滤器:

```
{  "glob":  "**/*.{jpg,jpeg}",  "use":  [  {  "name":  "lepto-resize",  "maxWidth":  1200  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在你所有的图片宽度都小于或等于 1200 像素。你可以发现更多的选项来使用储存库上的[`lepto-resize`](https://github.com/leptojs/lepto-resize):。

### 让我们添加另一个滤镜来创建我们的 png 的 Retina 版本:

```
{  "glob":  "**/*.png",  "use":  [  {  "name":  "lepto-resize",  "retina":  [2,  3]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`srcset` :
轻松地将它们插入到我们的 html 中

```
<img srcset="assets/output/icon.png,
             assets/output/icon@2x.png 2x,
             assets/output/icon@3x.png 3x"
     src="assets/output/icon.png" alt="Icon alt"> 
```

Enter fullscreen mode Exit fullscreen mode

我很确定你现在能够添加插件 [`lepto-webp`](https://github.com/leptojs/lepto-webp) 来创建你的 jpg 的 WebP 替代品了！查看这里的文档:[github.com/leptojs/lepto-webp](https://github.com/leptojs/lepto-webp)。

## 使用 GUI

为了获得最佳优化的图像，我们还需要仔细选择每个图像的最佳质量百分比。

默认情况下，GUI 可以在端口 4490 上访问，所以让我们运行`npm run build`并继续运行 [localhost:4490](http://localhost:4490/) 。我们将得到这个由三部分组成的漂亮界面:输入文件列表、过滤器列表和等待文件选择的优化器。

[![Lepto Graphical User Interface](img/6d696b2ab288a31fe97df8932182d99d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nGC7ENX1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hgu9mntesny4r2foncst.png)

让我们在列表中选择一个文件:它现在显示在优化器中，让我们可以使用质量滑块并实时查看输出。当您对质量百分比感到满意时，**单击按钮“添加优化插件”**和**通过单击“保存配置”**(或按⌘S / Ctrl+S)保存这个新配置。

[![Lepto GUI optimizer usage](img/2f25aed03248d8d8844f3c83dea3c563.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eA1ZUaLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3re5scayjzucsic181e5.png)

通过添加一个“优化插件”, GUI 简单地创建了一个新的过滤器，其 glob 等于文件名，插件对应于文件扩展名:

```
{  "glob":  "001.jpg",  "use":  [  {  "name":  "lepto.jpeg",  "quality":  48  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

确保将这些特定的过滤器放在列表的末尾。

## 更进一步

这个工具的用例是无穷无尽的。你甚至可以用 [`lepto-vibrant-color`](https://github.com/leptojs/lepto-vibrant-color) 插件**自动收集你的图像**的鲜艳颜色，并把它们放入一个 JSON 文件中。

[![lepto-vibrant-color extraction example](img/378553498724bac8dd9f5f248ae6dcdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yB_LKBNI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6fys66bblrizrfgo1pyj.png)

要了解关于 Lepto 的更多信息，请查看`lepto`和`lepto-cli`文档:

*   [github.com/leptojs/lepto](https://github.com/leptojs/lepto)

*   [github.com/leptojs/lepto-cli](https://github.com/leptojs/lepto-cli)

为了了解更多关于最佳图像优化的实践，我向你推荐由[艾迪·奥斯马尼](https://twitter.com/addyosmani) : [images.guide](https://images.guide/) 撰写的“[基本图像优化](https://images.guide/)”指南。

## 关于我

我是一名法国留学生，热衷前端开发好几年了。通过创建 Lepto，我尽力做一个不令人沮丧且高效的工具来轻松管理图像优化。出于性能和易用性的原因，构建命令行界面是显而易见的(参见[布兰德尔](https://twitter.com/brandur)的[这篇关于终端和设计](https://brandur.org/interfaces)的伟大文章)。然而，GUI 对于选择最佳压缩质量是必要的。于是我来到了这个大同。

我通过推特(Twitter)或电子邮件(T2)听取所有的建议、讨论和激烈的批评。

感谢阅读！😄

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [季米特里尼科拉斯](https://github.com/dimitrinicolas) / [轻子](https://github.com/dimitrinicolas/lepto)

### 通过 CLI 和 web 界面自动进行图像编辑、优化和分析。你给 lepto 你的输入和输出目录，你想要使用的插件和它们的选项。然后 lepto 做他的工作，你保留你的原始文件和输入目录的结构。一些插件甚至可以从你的图像中收集数据(比如原色),并将它们保存在一个 JSON 文件中。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Lepto: The best image optimizations practices made simple](img/a3fd07f64871c4421b8f00adf69e164b.png)T2】](https://raw.githubusercontent.com/dimitrinicolas/lepto/master/#get-started-with-cli--npm-scripts)

[![Build Status](img/ff7f847008c050a159f3a7e0cb557b45.png)T2】](https://travis-ci.org/leptojs/lepto)

这个工具的主要目的是自动化图像优化和分析这个项目是最近的，所以要小心使用它，我正在听取所有的反馈(我们可以通过 [twitter](https://twitter.com/dimitrincls) 交谈，不要跟着我，我从不发微博)。

**与 [ImageMin](https://github.com/imagemin/imagemin) 有什么区别？**我认为，如果你处理大型应用程序，那么就使用 ImageMin，但如果你正在创建小型静态网站，并且你想轻松优化你的资源，那么你可以尝试 lepto。

你给 lepto 你的输入和输出目录，你想要使用的插件和它们的选项。然后 lepto 做他的工作，你保留你的原始文件和输入目录的结构。一些插件甚至可以从你的图像中收集数据(比如原色),并将它们保存在一个 JSON 文件中。

如果你想了解更多关于图像优化的知识，我向你推荐令人惊叹的[图像](https://images.guide/)

</article>

[View on GitHub](https://github.com/dimitrinicolas/lepto)