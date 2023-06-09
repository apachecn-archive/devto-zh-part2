# 显示当前 git 分支

> 原文：<https://dev.to/czarpino/show-current-git-branch-17o0>

在开发过程中，总是知道您在哪个分支上是非常方便的，而不需要做 git 状态。大多数开发人员使用不必要的复杂 bash 脚本来修改 shell 的提示字符串 1 (PS1)变量。令人尴尬的是，我是大多数开发人员(我甚至有自己的回购，以“性能”为重点)。现在，我简单地使用一行程序。事实上，我只是浏览了这篇文章，并把下面的内容复制粘贴到我的`~/.bash_profile` :

```
# Show current git branch when entering a git repository
export PS1="\W  \[\e[0;36m\]\$(git rev-parse --abbrev-ref HEAD 2> /dev/null)\[\e[0m\]\$ " 
```

Enter fullscreen mode Exit fullscreen mode

*原载于[https://plog.czarpino.com/show-current-git-branch/](https://plog.czarpino.com/show-current-git-branch/)T3】*