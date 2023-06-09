# 存档 git 分支

> 原文：<https://dev.to/clsource/archiving-git-branches-3k70>

这是归档 git 分支的快速方法。也许你不再使用代码了，分支机构把回购弄乱了，但是出于备份或历史原因，你需要把它保存在某个地方。

第一件事是去分店结账

```
$ git checkout my-branch 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个新标签。我使用格式`archive/branch-{name}`

```
$ git tag archive/branch-my-branch 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将标签上传到 Github 或 Gitlab

```
$ git push origin --tags 
```

Enter fullscreen mode Exit fullscreen mode

并从本地和原始回购中删除该分支。

一定要像师父一样在另一个分支

```
$ git checkout master 
```

Enter fullscreen mode Exit fullscreen mode

然后

```
$ git branch -d my-branch
$ git push origin :my-branch 
```

Enter fullscreen mode Exit fullscreen mode

如果需要从本地删除标签(一个所有分支的备份)

```
$ git tag -d archive/branch-my-branch 
```

Enter fullscreen mode Exit fullscreen mode

或者从原点

```
$ git push --delete origin archive/branch-my-branch 
```

Enter fullscreen mode Exit fullscreen mode

这将清除所有标记的分支数据。请小心。