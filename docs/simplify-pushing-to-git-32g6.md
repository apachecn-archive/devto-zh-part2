# 简化向 git 的推送

> 原文：<https://dev.to/michalbryxi/simplify-pushing-to-git-32g6>

我想每个从命令行使用 *git* 的人都知道这个场景:

1)代码，代码，代码，...嗯，最好保存我的进度。

```
$ git checkout -b myawesomefeature
$ git add .
$ git commit -m 'It works!'
$ git push
fatal: The current branch myawesomefeature has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin myawesomefeature 
```

3) ! @#$%^&

然后你只需要手动输入上面冗长的命令就可以推送了。

这样做了几次后，我发现有一个更好的方法。您可以通过调整以下配置让 git 自动为您设置上游:

```
git config --global push.default current 
```

在这之后，简单的`git push`调用将把*当前的*分支推到一个*远程的*同名分支。

但是在设置之前，最好[阅读文档](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-config.html)并了解不同选项的作用:

*   current -推送当前分支以更新接收端同名的分支。在中心和非中心工作流中都有效。
*   上游——将当前分支推回到其更改通常集成到当前分支中的分支(称为@{upstream})。只有当您将数据推送到您通常从中提取数据的同一个存储库(即中央工作流)时，这种模式才有意义。
*   简单——在集中式工作流中，像 upstream 一样工作，如果上游分支的名称与本地分支的名称不同，还可以安全地拒绝推送。