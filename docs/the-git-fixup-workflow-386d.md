# Git 修复工作流

> 原文：<https://dev.to/koffeinfrei/the-git-fixup-workflow-386d>

> 原帖:
> 
> [www.koffeinfrei.org/2018/09/18/the-git-fixup-workflow/](https://www.koffeinfrei.org/2018/09/18/the-git-fixup-workflow/)

## 简介

GitHub 流或类似的 Git 策略已经成为许多开发团队使用 Git 和 GitHub 或 GitLab(甚至 Bitbucket)的标准方式，在这种策略中，主要工作是在特性分支上完成的，这些特性分支会产生 pull 或 merge 请求。除了这种工作方式的主要优点之外，它还能让开发者保持一个干净的 g it 历史(参见 [5 条让 Git 更好的规则](https://www.koffeinfrei.org/2016/11/10/5-rules-to-git-better/))。

让我们假设以下是本文建议的工作流程的起点:

*   您希望维护一个干净的 Git 历史，这意味着应该避免修复提交
*   您不希望因为必须一直重写您的 Git 历史而中断您的开发工作流
*   您只能在最近一次提交时使用`git commit --amend`

Git 有一些强大的命令，允许我们既保持一个干净的历史，又不必中断我们的开发工作流。我们称之为“Git 修复工作流”。

## 线性工作流

为了说明 Git 修复工作流，我们将使用下面的示例 hack 会话:

```
# coding the user component's index view

$ git commit -m "add user component's index view"

# coding the user component's edit view

$ git commit -m "add user component's edit view"

# updating the component's index view so it actually works

$ git commit -m "add user component's index view, it works now"

# a typo is noticed in the index view

$ git commit -m "fix typo in index view"

# fix a bug in the view rendering

$ git commit -m "fix view rendering"

$ rubocop
$ yarn lint

# fix linting errors

$ git commit -m "fix linting errors"

# team members do a code review on the PR / MR

$ git commit -m "fix import in index" 
```

Enter fullscreen mode Exit fullscreen mode

上面的会话导致了下面的 Git 历史:

```
fix import in index
fix linting errors
fix view rendering
fix typo in index view
add user component's index view, it works now
add user component's edit view
add user component's index view 
```

Enter fullscreen mode Exit fullscreen mode

以下提交反映了从另一个开发人员的角度来看相关的实际工作:

```
fix view rendering
add user component's edit view
add user component's index view 
```

Enter fullscreen mode Exit fullscreen mode

所有修改前一个提交的提交只会使历史变得混乱，应该被压制。例如，提交“修复索引视图中的拼写错误”应该与“添加用户组件的索引视图”放在一起。

为了清理历史，我们可以在合并分支之前手动交互地重新设置提交的基础。想象一下，虽然您可能有许多提交，但在这个过程的后期，您可能不记得哪些应该放在一起。此外，在交互式 rebase 模式中标记相关的提交需要大量的手工工作。

## 修正工作流

Git 包含了实现自动化所需的一切。因此，让我们用我们的新工具集重做之前的黑客会话:

```
# coding the user component's index view

$ git commit -m "add user component's index view"

# coding the user component's edit view

$ git commit -m "add user component's edit view"

# updating the component's index view so it actually works

$ git commit --fixup <COMMIT HASH OF THE COMMIT "add user component's index view">

# a typo is noticed in the index view

$ git commit --fixup <COMMIT HASH OF THE COMMIT "add user component's index view">

# fix a bug in the view rendering

$ git commit -m "fix view rendering"

$ rubocop
$ yarn lint

# fix linting errors, assuming the linting error was introduced in the commit
# "add user component's edit view"

$ git commit --fixup <COMMIT HASH OF THE COMMIT "add user component's edit view">

# team members do a code review on the PR / MR

$ git commit --fixup <COMMIT HASH OF THE COMMIT "add user component's index view"> 
```

Enter fullscreen mode Exit fullscreen mode

生成的 Git 日志如下所示:

```
fixup! add user component's index view
fixup! add user component's edit view
fix view rendering
fixup! add user component's index view
fixup! add user component's index view
add user component's edit view
add user component's index view 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`--fixup`标志来创建修正提交，而不是定期提交(并编写提交消息)。

现在，在合并合并请求之前，我们发布一个带有`--autosquash`标志的交互式 rebase。

> **注意:**
> GitLab 将包含修正提交的 MRs 标记为`WIP`，因此如果您没有挤压，合并将被阻止。

```
$ git rebase --interactive --autosquash \
  <COMMIT HASH OF THE COMMIT "add user component's index view">~1 
```

Enter fullscreen mode Exit fullscreen mode

我们将进入交互式重定基础模式，相关的提交已经标记为修复:

```
pick 364003b add user component's index view
fixup afa6970 fixup! add user component's index view
fixup 1da5d7c fixup! add user component's index view
fixup c998b08 fixup! add user component's index view
pick d9731b5 add user component's edit view
fixup 33d6080 fixup! add user component's edit view
pick 73e8a6a fix view rendering 
```

Enter fullscreen mode Exit fullscreen mode

生成的 Git 历史如下所示:

```
fix view rendering
add user component's edit view
add user component's index view 
```

Enter fullscreen mode Exit fullscreen mode

最终的结果是一个非常一致和可读的 Git 历史，其中每个其他开发人员都知道发生了什么，而没有您在开发时采取的无趣的回避步骤。

## 总结

总之，下面两个基本的 Git 命令是使用 Git 修复工作流所必需的:

```
$ git commit --fixup <COMMIT THAT NEEDS FIXING>
$ git rebase -i --autosquash <FIRST COMMIT THAT NEEDS FIXING>~1 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励:创建 Git 别名

如果你开始采用这种工作流程，你将不得不在你的终端上输入很多东西。此时，创建一些 Git 别名来放松您的大脑和双手是有意义的。

```
[alias]
    cif = commit --fixup
    ri = rebase -i --autosquash 
```

Enter fullscreen mode Exit fullscreen mode

## 更多奖励:全局自动撤销配置

该配置可以全局应用，而不必提供`--autosquash`标志。尽管如此，rebase 命令仍然需要`-i`。

```
$ git config --global rebase.autoSquash true 
```

Enter fullscreen mode Exit fullscreen mode

## 编辑(2018-09-18)

我写了两个 git 脚本`git fixup`和`git squash`来支持这个工作流，并添加了一篇描述它们的[博客文章](https://www.koffeinfrei.org/2018/09/22/git-fixup-and-git-squash/)。