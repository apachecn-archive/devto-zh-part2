# 解释了 3 个 GIT 生产力特性

> 原文：<https://dev.to/codevault/3-git-productivity-features-explained-4jbk>

我知道社区中有一些人每天都在使用 GIT，但是他们只是提交/拉/推，而忽略了它的一些更有用的特性。我向你展示了 3 个有用的特性，都与承诺有关，这将有助于你的生产力和改变的容易程度。

## ①`git stash`

这是最简单的方法，把你一直在做的事情暂时放一放，以利于紧急的 bug 修复。你要做的就是:

```
git stash
... do whatever it is needed to fix an urgent bug fix or whatever else
git stash pop 
```

在您`git stash`之后，您所有未提交的更改都将保存在一个临时位置，您可以使用`git stash pop`重新应用它。

## 2)使用多个本地分支

我知道人们一次又一次地说，任何有足够严肃的项目的人都应该为他们的项目使用多个分支。这并不总是适用于所有的项目，但是即使如此，如果您在上游只有一个分支，这并不意味着您不能在您自己的本地存储库中使用多个分支。你可以这样做:

```
git branch new-branch-name
git checkout new-branch-name
... commit whatever you want on this branch while keeping the main branch intact for more urgent modifications
git checkout master (or your main branch)
git merge new-branch-name 
```

瞧啊。如果需要，您还有另一种方法可以快速切换到另一个任务。这不是很好吗？

## 3)选择性提交

不用提交所有的更改，你可以有选择地选择哪些文件放在哪里，使用`git add`命令添加它们，使用`git commit -m ...`提交你添加的任何文件。

> 真的吗伙计？这就是你教的“生产力秘诀”？我从 5 岁开始就一直用它了！
> 
> 我知道我知道，但是首先，我犯了一个错误，我不知道 stage area 是做什么的，总是用`git commit -am ...`提交我所有的修改。很长一段时间我都不知道还有其他选择

话虽如此，步骤如下:

```
git add file_you_want_to_commit
git commit -m "Message properly explaining your changes"
git add another_file_you_want_to_commit
git commit -m "Another message properly explaining your changes" 
```

这就是本教程的全部内容。希望你从中有所收获。我发布了一个视频来更深入地解释这些特性。你可以在这里查看:

[https://www.youtube.com/embed/0SqmKR_jA0A](https://www.youtube.com/embed/0SqmKR_jA0A)

在开始使用 git 时，你还希望知道哪些特性？