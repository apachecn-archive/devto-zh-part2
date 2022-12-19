# 代码挑战:JumpStart 应用程序启动器

> 原文：<https://dev.to/lefebvre/just-code-challenge-jumpstart-app-launcher-1np0>

很久很久以前(1989 年)，我做的第一个应用程序是 Atari ST 的应用程序启动器，我称之为 JumpSTART。我最初是用 GFA BASIC 写的，后来用 Pascal (OSS Personal Pascal，技术上来说)重新实现了它。

当我得到我的第一个调制解调器时，我用 Genie 和 Delphi 上网并上传了免费的 JumpSTART。尽管它是免费软件，我还是收到了一些喜欢它的人寄来的支票。

当我看到我的码头变得拥挤时，我想起了 JumpSTART。我认为在 Xojo 中复制 JumpSTART 将是#JustCode 第 8 周的一个好项目。不过，这次我们姑且称之为快速启动。

这是 JumpSTART 在单色 640×480 屏幕上的样子:

[![](../Images/bd08db3117f2d25ab50cd9174473adfd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jBLdGI_E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/08/2018-08-09_14-06-09.png)

Xojo 中的 JumpStart 得到了极大的简化，如下所示:

[![](../Images/f48072894853d56042ec524be354e0ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFpYuwgG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.xojo.com/wp-content/uploads/2018/08/2018-08-09_14-10-06.png)

Xojo 版本自动保存和加载你添加的应用程序，所以不需要单独的加载/保存，重新读取按钮。从技术上讲，它们不一定是应用程序，因为即使是文档文件也可以启动。

添加应用程序按钮后，您可以右键单击它来重命名、更改或清除它，这样也就不需要一些 UI 控件了。你只需点击一个空按钮就可以添加一个应用程序。当你点击一个分配了应用程序的按钮时，应用程序就会启动，JumpStart 会隐藏自己。

这个 Xojo 应用程序演示了基于画布的按钮的使用，动态添加到窗口的 ContainerControls，以及保存/加载 JSON。基于画布的按钮将让我调整显示，颜色，也许会显示一个图标。

你可以在 GitLab 上[查看这个项目。](https://gitlab.com/xojo/JumpStart)

作为额外的代码奖励，我已经将我的[原始 JumpSTart Pascal 代码发布到 GitHub](https://github.com/paullefebvre/JumpSTART-AtariST) 。FWIW，原 Pascal 源码只有 1300 多行代码。Xojo 版 180 左右。

将您的#JustCode 项目添加到[第 8 周论坛对话](https://forum.xojo.com/49298-just-code-challenge-week-8-projects)中。