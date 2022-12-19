# 快速提示:为您的下一个项目自动生成脚手架🎯

> 原文：<https://dev.to/graphicbeacon/quick-tip-automatically-generate-the-scaffolding-for-your-next-project--2epl>

每次开始一个新的项目设置时，生成必要的文件和配置可能会变得令人厌烦。尤其是当您设定的内容与之前的项目没有任何不同时。

在这个快速技巧中，我将向您展示一个更快的替代方法，使用名为 **[stagehand](https://pub.dartlang.org/packages/stagehand)** 的包启动并运行 Dart 项目。

[https://www.youtube.com/embed/DbLZO3MLigE](https://www.youtube.com/embed/DbLZO3MLigE)
→ [**在 Youtube 上观看**](https://www.youtube.com/watch?v=DbLZO3MLigE)

Stagehand 是 Dart 项目脚手架生成器，灵感来自 Web Starter Kit 和 Yeoman 等主流工具。

现在假设你已经安装了 [Dart 版本 2](https://www.dartlang.org/tools/sdk#install)(`brew install dart --devel`)，你可以通过运行下面的命令来获取 stage hand 3.0:

```
$ pub global activate stagehand 
```

Enter fullscreen mode Exit fullscreen mode

这将从**pub**package management repo 中提取包。然后通过运行
来看看生成器模板选项

```
$ stagehand 
```

Enter fullscreen mode Exit fullscreen mode

目前，您可以生成这些模板:

1.  **console-full** -命令行应用程序示例。
2.  **package-simple**-Dart 库或应用程序的起点。
3.  **server-shelf** -使用 shelf 包构建的 web 服务器。
4.  **web-angular** -一个带有材料设计组件的 web app。
5.  **web-simple** -一个只使用核心 Dart 库的 web 应用。
6.  2D 动画和游戏的起点。

假设我们想要为一个 [AngularDart](https://pub.dartlang.org/packages/angular) 应用程序生成脚手架。我们可以通过运行下面的命令来做到这一点:

```
$ mkdir awesome_dart_project
$ cd awesome_dart_project
$ stagehand web-angular # or whichever template 
```

Enter fullscreen mode Exit fullscreen mode

这为我们提供了以下输出中的进一步说明:

[![Stagehand web-angular scaffolding output](../Images/ab5ec5bbbc7b57e86df77cd095b7695f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TnbNu3VK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i2ewygqrnh4uecrvql6r.png)

今天的快速提示就到这里。现在你试一下怎么样？👐☺️

**订阅[我的 Youtube 频道](https://www.youtube.com/channel/UCHSRZk4k6e-hqIXBBM4b2iA?view_as=subscriber)获取 Dart** 上的最新视频，谢谢！

**喜欢并关注我**😍更多关于 Dart 的文章。

## 快速链接

1.  [舞台工作包](https://pub.dartlang.org/packages/stagehand)
2.  [省道安装说明](https://www.dartlang.org/tools/sdk#install)
3.  **[免费飞镖截屏在 Egghead.io](https://egghead.io/instructors/jermaine-oppong)**