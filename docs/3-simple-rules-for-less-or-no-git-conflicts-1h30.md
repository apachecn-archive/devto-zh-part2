# 3 个简单的规则来减少或消除 git 冲突

> 原文：<https://dev.to/geshan/3-simple-rules-for-less-or-no-git-conflicts-1h30>

你用任何一种编程语言每天都要写至少 10 行代码吗？你在团队中工作吗？如果你对这两个问题的回答都是肯定的，那么即使你独自在一个项目中工作，你也需要学习。
[Git](https://git-scm.com/) 是最流行的版本控制系统，已经成为软件工程师必备的技能。

我见过团队在开始使用 git 和某种类型的
[gitflow](http://geshan.com.np/blog/2014/12/do-you-git-your-code-follow-this-simplified-gitflow-model/) 时陷入 git 冲突的陷阱。
当存在 git 冲突时，将分支合并到主分支成为一件痛苦的事情。
在这篇文章中，我将揭示 3 个简单的规则来避免 git 冲突。

[![3 simple rules for less or no git conflicts](../Images/a51a49f4d81a4d4d09ef332179b3152a.png "3 simple rules for less or no git conflicts")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---j3K6DDN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geshan.com.np/images/3-rules-git-conflict/git-conflict.png)

## 假设

您有一些 git 的工作知识。您遵循 git 分支模型，如 gitflow 或简化的 gitflow。

## 规则 1:保持你的变化小

这是避免团队中 git 冲突的黄金法则。当两个团队成员对同一行代码进行修改时，就会发生冲突。像 John 在 readme.md 中的变化像 Jack 在不同分支上的同一个
readme.md 文件中的变化像 7-15。有一个经验法则，每个拉取请求最多可以有 20 个文件
随着 200 行的增加而改变。如果变化少，重叠的机会就少。作为一个
副作用，它也将使部署和测试变更变得容易。

## 规则二:当你的主分支(一般是 master)发生变化时，用你的主分支(一般是 master)重新定基

当你的主分支发生变化时，改变你正在处理的分支的基础。通常`master`是
的主要分支，所以你最好每天至少跟师父换一次基。这可以避免你将其他团队成员的大量变更带入
。它的概念和上面的一样，一步一步的做小的改变，比一次做好很多倍。在一天结束时，考虑到
你在你的工作分支
，总是做以下事情

```
git checkout master
git fetch
git pull --rebase origin master
git checkout -
git rebase master 
```

Enter fullscreen mode Exit fullscreen mode

你得到最新的主人。然后你回到你以前的分支，用最新的主数据重新建立你的分支。

## 规则 3:更快地审查拉请求，并将它们合并到主分支

正如我之前所说的,“开放拉取请求(PR)至少在两个方面是一种责任。1 它是一项功能/修复程序，并未
提供给客户。2 它将很快引起代码冲突。”有一个规则，拉取请求需要在
打开它们的 3 天前进行操作。您可以检查代码，如果代码正常，请部署并合并，或者检查代码，修复问题，然后部署并合并。
这将有助于团队更快地完成任务，也不会让拉取请求几周都无人问津。

### 有用的提示

你遵循了上面的规则，还是陷入了 git 冲突的境地？使用`git cherry-pick`。如果您的工作分支中有多个
提交，首先[挤压](http://geshan.com.np/blog/2014/07/4-git-tips-beyond-basics/)
到一个提交。然后在 master/你的主分支之外新建一个分支，做`git cherry-pick <sha-of-your-squashed-commit>`。

假设您的工作分支`feature11`上只有 1 个提交，并且您现在在`feature11`上，执行以下操作:

```
git checkout master; git fetch && git pull --rebase origin master
git checkout -b feature-11-new
git cherry-pick 249bd9b150fdb1e6fc9e58af9823f70cc52579a3 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中,`249bd9b150fdb1e6fc9e58af9823f70cc52579a3`仅用于演示。分支`feature11`上的`git log -1`
可以知道你的 SHA 哈希

## 结论

没有什么灵丹妙药可以一直避免 git 冲突。你会不时地面对它。如果你每天都遇到 git 冲突，流程和系统需要改变。如果你有一份修改了 50 个文件和 700 个新行的 PR，你将面临冲突。

> 希望你少遇到 git 冲突，甚至完全避免。

* * *

最初发表于[Geshan.com.np](https://geshan.com.np)，你可以在那里读到更多的东西。