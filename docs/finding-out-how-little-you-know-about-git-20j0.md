# 发现你对 Git 知之甚少

> 原文：<https://dev.to/cseeman/finding-out-how-little-you-know-about-git-20j0>

我成为 Git 用户的时间不长，只是从一月份开始积极使用。这听起来似乎不是很长时间，但是对于一个开发人员来说，使用一个工具超过 5 个月几乎是一些项目所花费的时间(如果你幸运的话)或者一份合同的时间。自从从 Java 转到 Ruby，并转到一家更了解这方面的新公司，我知道了我们使用 Git 是多么的简单。

我们在 GitLab 上，在一个修改过的 GitHub 流程中使用 bash，有一个主和开发分支，所有的特性都是从开发分支出来的。我们确实有合并提交消息，并且有一些 CI/CD 集成从互联网可访问的 GitLab 拉到内部 GitLab。从 Subversion 的转换相当容易，需要一些重组来转换一些回购结构(我们与所有 project == new git repo 建立了一对一的关系，但这使得 waaaaaay 有太多的回购)。所以，我一直在做一些 Git，现在我知道我真的不知道如何做 Git。

我知道什么是 [Git 别名](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases)吗？没有。[换基](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)、[推带选项](https://developer.atlassian.com/blog/2015/04/force-with-lease/)或者 f [做回购](https://guides.github.com/activities/forking/)呢？不，不，不。

[![](../Images/187128c179d4f919b852eaf8d51c4f43.png)T2】](https://i.giphy.com/media/aH8oOOUfyrC8g/giphy.gif)

所以我现在有了一个 git 别名(我必须在记事本中调出这个别名，这样我才能记住所有的命令),并且我的提交消息必须更加具体(它们对于开发来说是可以的，但是当你准备好了你的拉请求时，压扁那些小东西),不要再直接拉和推我了！我需要记住再改基，再改基，再改基，为了所有好的东西，不要添加无关的提交消息，确保[修改](https://www.atlassian.com/git/tutorials/rewriting-history)。随着历史的变迁，我感觉自己就像一个时间旅行者。

[![](../Images/566231b267e8e50899d3ce1a093200c0.png)T2】](https://i.giphy.com/media/Vqvr9BGv1vhDi/giphy.gif)