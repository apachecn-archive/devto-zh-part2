# 第 002 集-项目结构加首次应用-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-002---project-structure-plus-first-application---aspnet-core-from-0-to-overkill-5dlc>

在这个帖子/视频中，我将开始设置项目，描述使用的存储库结构，并初始化第一个 ASP.NET 核心应用程序。应该又快又简单😉

鉴于此，下面的视频提供了一个概述，但如果你喜欢快速阅读，跳到书面综合。

[https://www.youtube.com/embed/ULUtAAHOyTw](https://www.youtube.com/embed/ULUtAAHOyTw)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。

## [T1】简介](#intro)

如前所述，在这篇文章中，我将只做项目的初始设置，浏览我将在。NET 资源库(剧透:将不会有。一路走来的网络仓库😛)并初始化第一个 ASP.NET 核心应用程序，即我们的示例(最终过度设计)系统的组管理组件。

我在 Mac 上做这个，所以有些命令可能是 bash 特有的。如果你使用的是 Windows 系统，请相应地进行调整。

## 创建知识库

首先，我去了 GitHub，在这个系列的伞状组织下为`GroupManagement`组件创建了一个新的存储库([AspNetCoreFromZeroToOverkill](https://github.com/AspNetCoreFromZeroToOverkill)),如上一篇文章所示。

正如我将为其他库所做的那样，我使用了 [MIT license](https://opensource.org/licenses/MIT) ，因为它很好，也很宽容，我只是为了好玩才这么做的🙂

除了许可之外，我选择了 readme 文件(仍然是空的，最终会填充它)而不是预定义的`.gitignore`文件，因为我有一个从 GitHub 提供的版本派生而来的另一个版本，并添加了一些额外功能。

在这之后，我只需要将回购克隆到我的电脑上`git clone https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement.git`。

## 知识库结构

对于存储库结构，我倾向于遵循类似于 David Fowler(ASP.NET 核心的微软架构师)在他的要点中推荐的东西: