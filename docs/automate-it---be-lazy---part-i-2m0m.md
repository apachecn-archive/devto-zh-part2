# 自动化 It——懒惰——第一部分

> 原文：<https://dev.to/khmarbaise/automate-it---be-lazy---part-i-2m0m>

基于我作为开源开发者的热情，特别是对 Apache Maven 项目的热情。我在[JIRA](https://issues.apache.org/jira/secure/BrowseProjects.jspa?selectedCategory=10510&selectedProjectType=all)工作，它帮助我们和我自己组织我们的工作，当然也跟踪报道的问题、功能要求等。

所以通常我会根据 JIRA 的某个特定问题开始我的工作，并遵循以下流程:

## 流程

1.  为自己创建一个问题(如果已经存在，转到步骤 2)
2.  根据问题在适当的项目中创建一个分支
3.  将问题分配给我自己
4.  将问题迁移到`IN PROGRESS`状态
5.  在分支上做一些工作并提交
6.  将分支推到远程，让[詹金斯检查分支](https://builds.apache.org/view/M-R/view/Maven/job/maven-box/job/maven-clean-plugin/)重复步骤 5，直到我完成我的工作。
7.  如果需要，根据主数据库重设基础
8.  将创建的分支合并到主分支中，并删除远程和本地分支。
9.  在注释中引用 commit，关闭相应的 jira 问题。

让我们看一个现实生活中的例子。典型的问题是 [MCLEAN-87](https://issues.apache.org/jira/browse/MCLEAN-87) ，该问题已经被处理并提交了一个适当的提交消息，该消息应该如下所示:

```
[JIRA-ISSUE] - Summary Text
Optional description 
```

Enter fullscreen mode Exit fullscreen mode

所以现实生活中的[提交看起来像这样](https://gitbox.apache.org/repos/asf?p=maven-clean-plugin.git;a=commitdiff;h=c6eed44352c9ab623836a9329b7645dbb61413bc) :

```
commit c6eed44352c9ab623836a9329b7645dbb61413bc (HEAD -> master, origin/master, m/master)
Author: Karl Heinz Marbaise <khmarbaise@apache.org>
Date:   Sat Jul 21 18:00:20 2018 +0200

    [MCLEAN-87] - Upgrade maven-plugins parent to version 32 
```

Enter fullscreen mode Exit fullscreen mode

如果您看一下流程步骤列表，就会发现这是一个巨大的步骤数。不幸的是，许多步骤都是手动的，既费时又容易出错。

所以大约四五个月前，我开始自动化/简化上述过程的一些步骤。

## 自动化步骤 1

在流程步骤 6 中，当我在一个分支上工作时，我必须一遍又一遍地将该分支的当前状态推送到远程，让 Jenkins 检查是否一切正常。此外，我可能需要将这个分支换成 master，后面必须跟一个`git push --force BRANCH`，但是`--force`是[非常危险的](https://developer.atlassian.com/blog/2015/04/force-with-lease/)，这意味着我必须使用`git push --force-with-lease BRANCH`来代替。我已经有了一个 git 运行的 bash 完成，它安全了大量的输入，但我想让它更舒适。

因此，我开始编写一个 bash 脚本，它包括以下步骤:

1.  检查我们是否在一个分支上只是为了确认一下。
2.  获取分支名称
3.  `git push origin --force-with-lease BRANCH`

所以这意味着最后我简单地[调用脚本](https://github.com/khmarbaise/automation-scripts/blob/f82324c08079df49cfdeb60539492b778c5f7445/gitpushwithlease.sh) :

```
$ gitpushwithlease.sh 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第一步完成了，这意味着有一点点进步，但还不够。

## 自动化第二步

因此，在做了一段时间后，我想到了第八步，这是一个非常打字和容易出错的步骤。让我总结一下这些步骤:

1.  检查我们是否在分支上
2.  获取分支名称
3.  结账大师
4.  只有在可以快进时才合并分支，否则会失败
5.  将更改推送到远程主机
6.  删除远程分支
7.  删除本地分支

结果是我的 [gitmergeandclean.sh](https://github.com/khmarbaise/automation-scripts/blob/5e3545d17805ab768edcafd89c4ba1e76af8b82e/gitmergeandclean.sh) 脚本，我这样使用:

```
$ gitmergeandclean.sh 
```

Enter fullscreen mode Exit fullscreen mode

输出如下所示:

```
~/ws-git-maven/plugins/maven-clean-plugin (MCLEAN-87)$ gitmergeandclean.sh
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
Updating 19b981e..c6eed44
Fast-forward
 pom.xml | 3 +--
 1 file changed, 1 insertion(+), 2 deletions(-)
Total 0 (delta 0), reused 0 (delta 0)
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-clean-plugin.git
remote:    19b981e..c6eed44  c6eed44352c9ab623836a9329b7645dbb61413bc -> master
remote: Syncing refs/heads/master...
To https://gitbox.apache.org/repos/asf/maven-clean-plugin.git
   19b981e..c6eed44  master -> master
remote: Sending notification emails to: ['"commits@maven.apache.org" <commits@maven.apache.org>']
remote: To git@github:apache/maven-clean-plugin.git
remote:  - [deleted]         MCLEAN-87
remote: Syncing refs/heads/MCLEAN-87 (FORCED)...
To https://gitbox.apache.org/repos/asf/maven-clean-plugin.git
 - [deleted]         MCLEAN-87
Deleted branch MCLEAN-87 (was c6eed44). 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第二步完成了，这意味着更多的改进，但还不够。

## 自动化第三步

如果我在一个分支上工作，我经常在它上面做几次提交，直到我认为我完成了。结果是一个包含 5、6 或更多提交的分支，这些提交应该被压缩到一个提交中，并带有一个好的提交消息和一个对我正在处理的问题的引用。

这可以通过以下步骤手动[和交互式](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History#_squashing)完成:

1.  计算您想要挤压的提交数量
2.  git rebase -i HEAD~NUMBEROFCOMMIT
3.  浏览编辑器，将`pick`替换为`s`进行挤压。
4.  安全并重写提交消息。

(我假设我错过了什么)。所以这真的很麻烦。所以我决定找一个脚本化的方法来处理这个问题。
经过一些实验后，我找到了一个解决方案，这就是我的[gitrebasebanch . sh 脚本](https://github.com/khmarbaise/automation-scripts/blob/4ecc12d800603d5538564c64cbd9513aa907e3b8/gitrebasebranch.sh)

```
$ gitrebasebranch.sh 
```

Enter fullscreen mode Exit fullscreen mode

在编辑器中，我只修改了提交消息。就是这样。这之后我可以做:

```
$ gitpushwithlease.sh 
```

Enter fullscreen mode Exit fullscreen mode

自动化的第三步完成了，这意味着更多的改进，但还不够。

这个故事将在我的下一篇文章中继续。