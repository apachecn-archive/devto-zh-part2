# 11 个让你哭泣的痛苦的面试问题

> 原文：<https://dev.to/fullstackcafe/11-painful-git-interview-questions-you-will-cry-on-1n2g>

[![11 Painful Git Interview Questions and Answers You Will Cry On](../Images/a1b6660722c040fc7ddecb4ce371de6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZdnrNCoZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/929382/pexels-photo-929382.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26h%3D350) 
根据最新的 Stack Overflow 开发者调查，超过 70%的开发者使用 Git，使其成为世界上使用最多的 VCS。Git 通常用于开源和商业软件开发，对个人、团队和企业都有很大的好处。

> 🔴最初发表于 [FullStack。咖啡馆-杀死你的技术&编码面试](https://www.fullstack.cafe/?utm_source=dev&utm_medium=blog)

### Q1:什么是饭桶叉？叉子、树枝和克隆的区别是什么？

> 题目:**git**T2】难度:⭐⭐

*   一个**分支**是一个存储库的远程的、服务器端的副本，不同于原始的。叉子并不是一个真正的 Git 概念，它更像是一个政治/社会概念。
*   一个**克隆**不是一个分叉；克隆是某个远程存储库的本地副本。当您克隆时，您实际上是在复制整个源存储库，包括所有的历史和分支。
*   一个**分支**是一种机制，用于处理单个存储库中的变更，以便最终将它们与代码的其余部分合并。分支是存储库中的东西。从概念上讲，它代表了一种发展思路。

🔗**来源:**【stackoverflow.com】T2

### Q2:“拉式请求”和“分支”有什么区别？

> 题目:**git**T2】难度:⭐⭐

*   一个**分支**只是代码的一个单独版本。

*   一个**拉请求**是当某人获取库，创建他们自己的分支，做一些改变，然后试图合并那个分支(把他们的改变放在其他人的代码库中)。

🔗**来源:**【stackoverflow.com】T2

### Q3:“git pull”和“git fetch”有什么区别？

> 题目:**git**T2】难度:⭐⭐

用最简单的话来说，`git pull`做了一个`git fetch`后跟一个`git merge`。

*   当你使用`pull`时，Git 试图自动为你做工作。**它是上下文相关的**，所以 Git 会将任何提取的提交合并到您当前工作的分支中。`pull` **自动合并提交，不需要你先检查**。如果你不密切管理你的分支机构，你可能会遇到频繁的冲突。

*   当您`fetch`时，Git 从目标分支收集当前分支中不存在的任何提交，**将它们存储在您的本地存储库**中。然而，**并没有将它们与你当前的分支**合并。如果您需要保持您的存储库是最新的，但是如果您更新您的文件，您正在处理的东西可能会中断，这是非常有用的。要将提交集成到您的主分支中，您可以使用`merge`。

🔗**来源:**【stackoverflow.com】T2

### Q4:如何在 git 中恢复之前的提交？

> 题目:**git**T2】难度:⭐⭐⭐

假设你有这个，其中 C 是你的头，(F)是你的文件的状态。

```
 (F)
A-B-C
    ↑
  master 
```

Enter fullscreen mode Exit fullscreen mode

*   要取消提交中的更改:

```
git reset --hard HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

现在 B 是头。因为您使用了- hard，所以您的文件被重置为提交 b 时的状态。

*   要撤消提交但保留您的更改，请执行以下操作:

```
git reset HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

现在我们告诉 Git 将头指针向后移动一次 commit (B ),让文件保持原样,`git status`显示您已经签入 c 的更改。

*   撤消提交，但保留文件和索引

```
git reset --soft HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

当您执行`git status`时，您将会看到和以前一样的文件在索引中。

🔗**来源:**【stackoverflow.com】T2

### Q5:什么是“git cherry-pick”？

> 题目:**git**T2】难度:⭐⭐⭐

命令 git *cherry-pick* 通常用于将特定的提交从存储库中的一个分支引入到另一个分支。常见的用途是将提交从维护分支向前或向后转移到开发分支。

这与其他方式形成对比，例如合并和 rebase，它们通常将许多提交应用到另一个分支上。

考虑:

```
git cherry-pick <commit-hash> 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【stackoverflow.com】T2

### Q6:解释分叉工作流的优势

> 题目:**git**T2】难度:⭐⭐⭐

**分叉工作流**与其他流行的 Git 工作流有着本质的不同。它没有使用单一的服务器端存储库作为“中央”代码库，而是为每个开发人员提供了他们自己的服务器端存储库。分叉工作流在公共开源项目中最常见。

分叉工作流的主要优势是贡献可以被集成，而不需要每个人都推到一个单一的中央存储库，这导致了一个干净的项目历史。开发人员推送到自己的服务器端资源库，只有项目维护人员可以推送到官方资源库。

当开发人员准备好发布本地提交时，他们将提交推送到他们自己的公共存储库——而不是官方存储库。然后，他们向主存储库提交一个 pull 请求，这让项目维护人员知道一个更新已经准备好被集成了。

🔗**来源:**【atlassian.com】T2

### Q7:说说 HEAD，working tree，index 的区别，在 Git？

> 题目:**git**T2】难度:⭐⭐⭐

*   **工作树/工作目录/工作区**是您看到和编辑的(源文件)目录树。
*   **索引/暂存区**是/中的一个大型二进制文件。git/index，它列出了当前分支中的所有文件、它们的 sha1 校验和、时间戳和文件名——它不是另一个包含文件副本的目录。
*   **HEAD** 是对当前检出分支中最后一次提交的引用。

🔗**来源:**【stackoverflow.com】T2

### 问题 8:你能解释一下 Gitflow 的工作流程吗？

> 题目:**git**T2】难度:⭐⭐⭐

Gitflow 工作流使用两个并行的*长期运行的*分支来记录项目的历史，`master`和`develop`:

*   主片 -随时准备在现场发布，一切都经过全面测试和批准(生产就绪)。

    *   **热修复**——维护或“热修复”分支用于快速修补生产版本。热修复分支很像发布分支和特性分支，除了它们是基于`master`而不是`develop`。
*   **开发**——是所有特性分支合并到的分支，也是所有测试执行的地方。只有当所有东西都被彻底检查和修复后，它才能被合并到`master`中。

    *   **特性**——每个新特性应该驻留在自己的分支中，可以作为它们的父特性推送到`develop`分支。

[![Gitflow workflow](../Images/a7888946e584e94c8727a6d6821a74af.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pLQxGakq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wac-cdn.atlassian.com/dam/jcr:61ccc620-5249-4338-be66-94d563f2843c/05%2520%282%29.svg%3FcdnVersion%3Dji)

🔗**来源:**【atlassian.com】T2

### Q9:什么时候该用“git stash”？

> 题目:**git**T2】难度:⭐⭐⭐

`git stash`命令获取您未提交的更改(暂存的和未暂存的)，保存它们以备后用，然后从您的工作副本中恢复它们。

考虑:

```
$ git status
On branch master
Changes to be committed:
new file: style.css
Changes not staged for commit:
modified: index.html
$ git stash
Saved working directory and index state WIP on master: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage
$ git status
On branch master
nothing to commit, working tree clean 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 stashing 的一个地方是，如果我们发现我们在上一次提交中忘记了一些东西，并且已经在同一个分支中开始处理下一个

```
# Assume the latest commit was already done
# start working on the next patch, and discovered I was missing something

# stash away the current mess I made
$ git stash save

# some changes in the working dir

# and now add them to the last commit:
$ git add -u
$ git commit --ammend

# back to work!
$ git stash pop 
```

Enter fullscreen mode Exit fullscreen mode

🔗**来源:**【atlassian.com】T2

### Q10:如何在不从你的文件系统中移除文件的情况下，从 git 中移除文件？

> 题目:**git**T2】难度:⭐⭐⭐⭐

如果您在`git add`期间不小心，您可能会添加您不想提交的文件。然而，`git rm`会将它从您的临时区域(索引)和文件系统(工作树)中删除，这可能不是您想要的。

而是使用`git reset` :

```
git reset filename          # or
echo filename >> .gitingore # add it to .gitignore to avoid re-adding it 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`git reset <paths>`是`git add <paths>`的反义词。

🔗**来源:** [codementor.io](https://www.codementor.io/citizen428/git-tutorial-10-common-git-problems-and-how-to-fix-them-aajv0katd)

### Q11:什么时候用“git rebase”代替“git merge”？

> 题目:**git**T2】难度:⭐⭐⭐⭐⭐

这两个命令都是为了将一个分支的变更集成到另一个分支中而设计的——它们只是以非常不同的方式完成。

合并/重定基数前考虑:

```
A <- B <- C    [master]
^
 \
  D <- E       [branch] 
```

Enter fullscreen mode Exit fullscreen mode

`git merge master`之后:

```
A <- B <- C
^         ^
 \         \
  D <- E <- F 
```

Enter fullscreen mode Exit fullscreen mode

`git rebase master`之后:

```
A <- B <- C <- D <- E 
```

Enter fullscreen mode Exit fullscreen mode

使用 rebase，你说使用另一个分支作为你工作的新基础。

**何时使用:**

1.  如果你有任何疑问，使用合并。
2.  基于你希望你的历史看起来像什么来选择改变基础或合并。

**需要考虑的更多因素:**

1.  **您正在获取变更的分支是否与您团队之外的其他开发人员共享(例如，开源、公共)？**如果是这样，不要改变。Rebase 破坏了分支，那些开发人员将会有破损/不一致的库，除非他们使用`git pull --rebase`。
2.  你的开发团队有多熟练？ Rebase 是一个破坏性的操作。这意味着，如果您没有正确地应用它，您可能会丢失提交的工作和/或破坏其他开发人员的存储库的一致性。
3.  分支本身代表有用的信息吗？一些团队使用*每个特性分支*模型，其中每个分支代表一个特性(或者 bugfix，或者子特性，等等)。)在这个模型中，分支帮助识别相关提交的集合。在*每个开发人员分支*模型的情况下，分支本身不传递任何附加信息(提交已经有了作者)。重定基数没有坏处。
4.  **您可能出于任何原因想要恢复合并吗？**与恢复合并相比，恢复(如撤销)rebase 相当困难和/或不可能(如果 rebase 有冲突)。如果你认为有机会，你会想要恢复，然后使用合并。

🔗**来源:**【stackoverflow.com】T2

> 谢谢🙌阅读，祝你面试好运！
> *查看更多 FullStack 面试问题&答案上👉 [www.fullstack.cafe](https://www.fullstack.cafe)*