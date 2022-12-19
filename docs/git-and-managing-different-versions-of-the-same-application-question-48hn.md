# Git 和管理同一个应用问题的不同版本

> 原文：<https://dev.to/jackharner/git-and-managing-different-versions-of-the-same-application-question-48hn>

我正在开发 Chrome/Firefox 扩展。我在用 Chrome 的时候就开始做了。从那以后，我开始更多地使用火狐浏览器。Chrome 网上商店已经提供了这个扩展，我也想让它在 Firefox 上也可以使用。

在我的~~懒惰~~冲刺 MVP 的过程中，我最终只是将回购分支到`master-ff`分支，以进行与 Firefox 相关的更改。这带来了相当大的变化，有些并不是针对 Firefox 版本的，现在需要移植回 Chrome 版本。我能真正向前看的唯一方法是回顾对`master-ff`的提交，看看什么需要回到`master`，合并/复制变更。

> 我是不是又笨又懒，或者这是处理同一件事情不同版本的有效方法？
> 
> 处理这种情况还有其他建议吗？两份独立的报告？Git 但是我做的不对？

*照片由[布丽娜·布鲁姆](https://unsplash.com/photos/Bb_X4JgSqIM)在 Unsplash* 上拍摄