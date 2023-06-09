# 简单的 git 回扣

> 原文：<https://dev.to/jbutz/simple-git-rebases-17d5>

学会恰当地使用`git rebase`可以帮助您确保您的 git 提交日志有意义并且易于遵循。如果您想使用像`git bisect`这样的命令，这也很重要。

最近，为了调试一个问题，我不得不提交 47 次。由于 IP 白名单的原因，我只能在 Heroku 私有空间内测试我的更改。所以一次又一次的提交被推送到 Heroku，但是我从来没有把这些提交推送到 GitHub。有时强制推送到 Heroku 是必要的，但很少允许对 GitHub 或任何存储库的地方做同样的事情。

今天，我可以进去做一个交互式的 rebase，将这 47 个提交合并成一个带有一个很好的日志消息的提交，并将其推送到 GitHub。我做的一件很有帮助的小事是用`[DEBUG]`为每一个调试提交一致地启动提交消息。当我重定基准时，我能够清楚地看到我的调试工作从哪里开始，并且不必担心如何命名我的提交。下面我将带你通过一个简单的例子来展示我是如何重定基础的。

让我们假设你遇到了和我一样的情况，正在 Heroku 上调试问题，不得不继续提交。您的调试运气更好，只提交了 10 次，但是您并不真的需要每个人都看到您在调试中经历的每一步。

首先，我们的提交日志看起来像这样:

```
> git log --oneline
b481c12 (HEAD -> feature-cool-stuff, heroku/master) [DEBUG] Remove debugging code
b47a33f [DEBUG] Lock package2 version
fdf7e4f [DEBUG] Downgrade package2, Upgrade package1
c8a3a97 [DEBUG] Downgrade package1
ccdcda3 [DEBUG] Tweak even more
723e090 [DEBUG] Tweak complex function
23f4f6e [DEBUG] Moar logging!
39c00f8 [DEBUG] console.log not console.olg
749df59 [DEBUG] Add even more log output
2a16981 [DEBUG] Add console debugging statements
e2399fe (origin/feature-cool-stuff) Upgrade packages
.... 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将运行`git rebase -i`开始对我们当前的分支进行交互式的基础调整。这将在编辑器中显示:

```
pick e2399fe Upgrade packages
pick 2a16981 [DEBUG] Add console debugging statements
pick 749df59 [DEBUG] Add even more log output
pick 39c00f8 [DEBUG] console.log not console.olg
pick 23f4f6e [DEBUG] Moar logging!
pick 723e090 [DEBUG] Tweak complex function
pick ccdcda3 [DEBUG] Tweak even more
pick c8a3a97 [DEBUG] Downgrade package1
pick fdf7e4f [DEBUG] Downgrade package2, Upgrade package1
pick b47a33f [DEBUG] Lock package2 version
pick b481c12 [DEBUG] Remove debugging code

# Rebase e2399fe.. b481c12 onto e2399fe (11 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
#   However, if you remove everything, the rebase will be aborted.
#
#
# Note that empty commits are commented out 
```

Enter fullscreen mode Exit fullscreen mode

第一次调试提交的命令应该是`reword`。其余的都应该改成有`fixup`命令。这将所有的调试提交合并成一个，忽略它们最初的提交消息，并允许我设置结束消息。

```
pick e2399fe Upgrade packages
reword 2a16981 [DEBUG] Add console debugging statements
fixup 749df59 [DEBUG] Add even more log output
fixup 39c00f8 [DEBUG] console.log not console.olg
fixup 23f4f6e [DEBUG] Moar logging!
fixup 723e090 [DEBUG] Tweak complex function
fixup ccdcda3 [DEBUG] Tweak even more
fixup c8a3a97 [DEBUG] Downgrade package1
fixup fdf7e4f [DEBUG] Downgrade package2, Upgrade package1
fixup b47a33f [DEBUG] Lock package2 version
fixup b481c12 [DEBUG] Remove debugging code 
```

Enter fullscreen mode Exit fullscreen mode

在保存并退出编辑器后，我看到了一条提交消息，我对它进行了修改，以反映结果提交实际包含的内容。在保存并退出之后，rebase 继续执行，直到留给我一个更简单的提交日志。

```
> git log --oneline
b4fac12 (HEAD -> feature-cool-stuff) Fix bugs caused by package upgrade
e2399fe (origin/feature-cool-stuff) Upgrade packages
.... 
```

Enter fullscreen mode Exit fullscreen mode

如果你不小心或者不明白你在做什么，重置基础可能是一个危险的任务，但是只要一点时间和知识，你就可以利用这个强大的特性。