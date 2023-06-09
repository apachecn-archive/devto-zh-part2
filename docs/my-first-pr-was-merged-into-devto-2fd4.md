# 我的第一个 PR 被并入 dev.to！

> 原文：<https://dev.to/siideemt/my-first-pr-was-merged-into-devto-2fd4>

([日文原创博文](https://ontheneworbit.blogspot.com/2018/11/devto.html)

我的第一个开源项目公关成功合并。它合并的回购是 dev.to！

[通过 sidemt Pull Request # 1121 the practical dev/dev . to](https://github.com/thepracticaldev/dev.to/pull/1121)将 GitLab URL 字段添加到用户配置文件

[![Merged PR](img/090af13b4877abdca284d7b8673454a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8tGJC9Xf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xwujv4qrus9wwv36wpy6.PNG)

## 功能我添加

我添加了一个新的字段，在 DEV profile 上放置了一个到 GitLab profile 的链接。

[![GitLab link on DEV profile](img/03ab1085e9b85140983d6a84f59e7f10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3OQmf1Pp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lsmie7wmfc1sv1o6dcm2.png)

@ben 在 GitHub 上给了我一个评论，所以我也为它写了更新日志。

[Changelog:将 GitLab 链接添加到您的 profile - DEV 社区](https://dev.to/siideemt/changelog-add-gitlab-link-to-your-profile-3fn4)

## 我的想法

很多人可能知道 dev.to 使用 Ruby on Rails。我已经学习了 Rails 的基础知识，并制作了一些 web 应用程序作为个人项目，但从未参与过如此大的项目。但是已经有类似的 URL 字段，其中一个是最近添加的，所以我可以通过查看其他字段的 PRs 和代码很容易地找到我需要更改的内容。

对我来说，最困难的部分是在本地设置环境。

### Ruby on Rails on Windows

我是 Windows 用户。

据我所知，我无法在 Windows 上安装`rbenv`。所以我用了 WSL(Linux 的 Windows 子系统)。

*   Ruby (on Rails)的环境安装在 WSL(Ubuntu)上
*   PostgreSQL 安装在 Windows 上
*   从 dev.to repo 克隆的代码存储在 Windows 上

在 dev.to 的文档中有一个[详细的指南。这对我帮助很大。](https://docs.dev.to/installation/windows/)

### 哪里卡住了

我正在考虑写一些详细的帖子，告诉他们我是如何解决这些问题的。

*   在本地启动开发之前进行初始安装
*   无法在本地环境中登录(感谢 [@andy](https://dev.to/ben) 的帮助！)
*   登录开始工作，但是相反，Rubocop 有一个 Ruby 版本不匹配的错误，这阻止了我进行新的提交(它在预提交钩子中)
*   由`rbenv`管理的 Ruby 版本似乎有问题，但我无法解决。研究了一整天之后，我决定重置 WSL 并重新安装所有东西(我可以这样做，因为 dev.to 是我使用 WSL 的唯一项目)

经历了这些挑战，我终于提交了 PR。

### 我学到了什么

我添加的代码大约有 30 行。我 90%的工作是搭建环境。

但是作为一个没有开发工作经验的代码新手，我从这个安装过程中学到了很多。

当你自己创造东西时，你会使用你知道或听说过的东西。经历了所有的错误，我有很多机会去处理我不知道是什么的事情。

许多“我甚至不知道它们存在的东西”变成了“我听说过它们的名字的东西”和“我有点知道它们做什么的东西”。我现在可以猜测“我也许可以用这个来做这件事……”并且更容易地搜索它。

如果你是编程初学者，我推荐你尝试搭建一个大型开源项目的开发环境。即使你最终无法提交一份 PR，你也可以了解一个真实世界的项目是什么样子，你可以练习阅读错误信息和搜索解决方案，你可以看到大量模块的名称，这些名称可能在(不久的)将来对你有所帮助。

事实上，一开始我考虑向 [freeCodeCamp](https://github.com/freeCodeCamp/freeCodeCamp) 提交我的第一份 PR(但是我找不到“欢迎第一次”的问题。我很幸运地在这里找到了一个入门级的问题！所以我最近也安装了它的环境。这些经历让我想到，对于初学者来说，建立一个开源项目的本地环境可能是一个很好的实践。

## GitLab 用户！

我使用 GitLab 作为我的私人存储库。免费的！

如果您是 GitLab 用户，请尝试在您的开发人员个人资料中添加 GitLab 的链接！