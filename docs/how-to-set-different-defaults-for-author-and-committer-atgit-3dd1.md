# 如何在 git 中为作者和提交者设置不同的默认值

> 原文：<https://dev.to/igorsantos07/how-to-set-different-defaults-for-author-and-committer-atgit-3dd1>

> [![Me, looking for a way to set different author and committer on Google: nowhere to be found](img/a254fde1ece6ef98ad24dd48c8520631.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eJvBMGI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/seruk4cobw387yhgyfum.jpg) 
> *我，想办法在 Google 上设置不同的作者和提交者:无处可寻。悲伤的伊戈尔很悲伤。*

我希望有一个不同于默认作者的默认提交者，因为我需要区分来自本地和测试环境的提交。

这不像在 git 中设置名称/电子邮件那样直接，因为[没有针对作者和提交者的单独配置条目](https://git-scm.com/docs/git-config#git-config-useremail)。然而，如果你[覆盖一些环境变量](https://git-scm.com/docs/git-commit-tree#_commit_information)，仍然是可能的，如下:
示例(在你的`~/.bashrc`或`~/.bash_profile` ):

```
GIT_COMMITTER_NAME=”Deploy server”
GIT_COMMITTER_EMAIL=”deploy@server.dev” 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果您已经忘记/不知道，您可以通过编辑`~/.gitconfig`或运行如下命令来设置 git 提交的默认名称/用户:

```
$ git config — global user.name “Igor Santos”
$ git config — global user.email “igor.santos@example.com” 
```

Enter fullscreen mode Exit fullscreen mode