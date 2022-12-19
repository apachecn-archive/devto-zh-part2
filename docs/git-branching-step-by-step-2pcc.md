# Git 分支，循序渐进

> 原文：<https://dev.to/thadevelyouknow/git-branching-step-by-step-2pcc>

[![git](../Images/412d0da14a6b8d6cbef3808933414bef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zeO02K8M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/71bk4rcbfnebny5hful3.png)

```
"Stop.

Collaborate and Listen." ~ V. Ice 
```

Enter fullscreen mode Exit fullscreen mode

##### 
  
强烈建议您为您的 shell 安装某种装饰器(bash 或 zsh。)这将有助于你永远知道自己属于哪个部门，而且看起来更轻松。许多人使用 iTerm。我用 curl 安装 oh-my-zsh:访问T7】

## 第一步

> 如果您拥有共享存储库的协作者权限，请执行以下操作:

在您的终端中，键入:

`git status`

你想多频繁就多频繁。它将为您在任何类型的 git 工作流中的后续步骤提供线索。`git status`、早而常。

## 第二步

我确保我在团队共享存储库的主分支中&我有最新的版本:

`git checkout master`

`git pull origin master`

## 第三步

我有自己的分店。你可以从 Github 站点(或者 Bitbucket 或者 Azure DevOps Repos)创建一个，但是我更喜欢在 Terminal/bash 中创建。我给它起了一个不带空格的名字，比如:“mybranchname”。最好以您正在处理的功能或修复来命名它。

`git checkout -b myNEWbranchname`

现在我在我的分支，不是主，所以做改动是安全的。

## 第四步

我在我的代码编辑器里不停地写代码。

## 第五步

我添加我的更改:

`git add path/to/file-name-here` *

*提示:通过运行 git status 找到已更改文件的名称路径

或者作为快捷方式，添加所有文件:

`git add .`

## 第六步

然后我提交更改:

`git commit -m 'my awesome improvements to our app'`

## 第七步

如果我只想将更改推送到我的分支的本地副本(参见下面的步骤 8，了解第一次如何连接它)

`git push`

## 第八步

第一次推到分支？我希望将更改保存在我的本地机器上，但是我还希望对我的分支的远程引用也能反映它们，所以每次提交都会被跟踪。

这是一个常见的错误——分支可能会不同步。请记住，git 版本控制在您的机器中，但最重要的是位于另一台服务器上的共享 repo。远程分支和本地分支需要始终相互反映。

`git branch --set-upstream-to=origin/mybranchname mybranchname`

## 第九步

如果你跑步的话，你会知道:

`git push`

## 第十步:协作

但是等一下。我想刚才有人在做一个专题。我不认为我有最新的主代码。我希望在发出拉取请求之前，提前解决与主分支机构的冲突。

所以我:

是的，就在我的分行里！

这将获取我的父分支(在这个例子中是 master，但也可以是其他父开发分支)并将其与我当前的分支合并！请记住现在修复任何合并冲突。

## 第 11 步

如果你再跑一次`git status`，你就会知道:

`git push`将合并的变更推送到您当地的分支机构。

如果我得到一个提示，说我需要一个提交消息来解释合并，我可以用

`Shift Z Z`

## 第 12 步

我去我的远程存储库[github.com](https://github.com)(ot bit bucket 或 Azure DevOps Repos)

在那里，我看到“拉请求”，所以我点击进入该页面。

## 第十三步

我单击我的拉动请求将其打开，然后单击确认(或批准)按钮，再单击一次，直到我满足了确认合并*的所有选项。如果有冲突，我会在确认一切和/或完成合并之前解决它们。

*如果您正在部署具有连接到 PR(拉请求)的自动化的管道，可能会有更多的步骤。这超出了本文的范围。

## 第 14 步

您将被要求删除该分支。这是一个最佳实践，尽管许多人避免这样做。运用你的判断力。如果你保留你的分支，在开始任何新的工作之前，确保你总是与师父保持同步。最佳实践是对错误修复和特性使用新的、描述性命名的分支，然后在合并后删除它们。

## 第十五步

在我的终端(或 git bash)中，我将远程主代码(以及提交历史和其他分支的所有引用)放入我的本地分支&再次推送。我的本地分支应该总是与远程回购中发生的事情保持同步。从我的分支内部:

`git pull origin master`
`git push`(给我的分支)

## 第十六步

如果我在 PR 之后删除了我在站点上的分支，在我的终端中，我删除了分支的*本地*版本。

`git branch -d mybranchname`

## 第十七步

我想创建一个新的分支，并从第一步重新开始这个循环。

冲洗。重复一遍。