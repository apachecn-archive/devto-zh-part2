# Git 合并来自另一个分支的特定文件

> 原文：<https://dev.to/alexruzenhack/git-merge-specific-file-from-another-branch-dl>

有两个团队在同一个项目中工作，他们意识到一个共同的问题已经被一方解决了，但是每个分支中的工作状态都是`in progress`并且进行合并可能是不安全的。

> 如何解决这个问题？

有一个安全的解决方案:*合并另一个分支的特定文件。*还有就是步骤:

```
$ git checkout <another-branch> <path-to-file> [<one-more-file> ...]
$ git status
$ git commit -m "'Merge' specific file from '<another-branch>'" 
```

就是这样！

这里有一个更详细的方法。