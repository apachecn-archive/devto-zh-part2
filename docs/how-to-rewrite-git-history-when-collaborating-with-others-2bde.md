# 与他人合作时如何重写 git 历史

> 原文：<https://dev.to/cassidycodes/how-to-rewrite-git-history-when-collaborating-with-others-2bde>

我最近在做一个新的节点项目，当我第一次测试时，我将 dist 目录的内容提交给了 git。后来，我用 Docker 设置了这个项目，我意识到我不想让 dist 出现在 repo 中，因为它会增加 Docker 图像的大小。

从 git 中移除文件有点棘手。您可以将它添加到。gitignore，但是一旦 git 正在跟踪一个对象，git 将一直跟踪它的变化，即使它在 gitignore 中。我把 Dalibor Nasevic 的文章保存在我的书签里，以备不时之需。但是为了从我们的项目中删除 dist，我们需要重写多个分支的历史，以确保这里的文件不会再次出现

以下是我们在多个分支上重写历史的步骤:

结账大师。

```
git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

将`dist/`加到`.gitignore`上。

重写主服务器上的历史以忽略`dist`目录。请注意，这将重写每次提交的 SHA。您的分支现在将看起来像一组完全不同的 git 提交。

```
git filter-branch --tree-filter 'rm -rf dist' HEAD 
```

Enter fullscreen mode Exit fullscreen mode

查看您的 repo 中是否有任何对象指向 dist。您应该在这里的输出中看到您的分支名称。

```
git for-each-ref --format='delete %(refname)' refs/original 
```

Enter fullscreen mode Exit fullscreen mode

通过终止 reflog 并强制 GC 来取消对对象的引用。

```
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now 
```

Enter fullscreen mode Exit fullscreen mode

力推新的总支。

```
git push origin master --force 
```

Enter fullscreen mode Exit fullscreen mode

现在，其他开发人员需要遵循类似的步骤来更新他们的分支。他们可以检查他们的工作分支并遵循上面的步骤，但是不要推进他们的分支。这些步骤将从它们的历史中删除`dist`目录，但是它们将重写分支历史中的每个 SHA，所以新的主分支和它们的分支对 git 来说看起来是不同的。为了解决这个问题，我们将挑选他们提交到一个新的分支。

使用此命令查找要检查的 sha 范围。如果您的分支中有超过 50 个提交，您可以删除`-n 50`选项。抄写你作品的第一页和最后一页。

```
git log —pretty=oneline —graph —abbrev-commit -n 50 
```

Enter fullscreen mode Exit fullscreen mode

从新的历史开始

```
git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

并从
分支

```
git checkout -b your-branch-new-history 
```

Enter fullscreen mode Exit fullscreen mode

最后，将您的提交精选到您的新分支上

```
git cherry-pick <starting sha>..<ending sha> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以删除原来的工作分支，并将您的新工作推送到 GitHub。一旦您修复了所有的工作分支，您就可以通过列出 git 曾经跟踪过的所有文件(包括删除的文件)来验证`dist`目录已经不在您的历史中了:

```
git log --pretty=format: --name-only --diff-filter=A | sort - | sed '/^$/d' | grep dist 
```

Enter fullscreen mode Exit fullscreen mode