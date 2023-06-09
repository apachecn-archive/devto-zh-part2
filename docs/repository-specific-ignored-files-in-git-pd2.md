# Git 中特定于存储库的被忽略文件

> 原文：<https://dev.to/hoelzro/repository-specific-ignored-files-in-git-pd2>

*最初发表于[hoelz . ro](https://hoelz.ro/blog/repository-specific-ignored-files-in-git)T3】*

您是否曾经在 Git 存储库中工作，希望 Git 命令像`git status`一样忽略某些文件，但是您不想用您特定的忽略规则污染项目的`.gitignore`文件？嗯，有了`.git/info/exclude`，你就可以了！

假设您想要忽略一个名为`notes`的文件。我经常这样做，因为我不喜欢在修改项目笔记时污染修订历史。

而不是这样做:

```
 $ echo notes >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

这样做:

```
 $ echo notes >> .git/info/exclude 
```

Enter fullscreen mode Exit fullscreen mode

`.git/info/exclude`永远不会在存储库之间共享，所以你可以自己保存一些文件，而不需要来自`git status`和朋友的额外输出。

您也可以使用`~/.gitconfig`添加特定于您的计算机的忽略模式；只需添加以下内容(或类似内容):

```
[core]
  excludesfile = /home/myuser/.gitignore 
```

Enter fullscreen mode Exit fullscreen mode

现在`/home/myuser/.gitignore`也会被咨询忽略模式！