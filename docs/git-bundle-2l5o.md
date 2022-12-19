# git bundle

> 原文：<https://dev.to/gabeguz/git-bundle-2l5o>

前几天我在工作中遇到了一个有趣的问题。我们雇佣了一些外部承包商来为我们开发一个 android 应用程序，现在我们需要从他们那里获得代码，并放入我们的私有 GitHub。问题是，我不能很快让他们接触到回购，因为获得这些批准需要相当多的过程。他们也没有足够的席位将我添加到他们的 GitHub 帐户，所以我们不得不要求他们发送一个包含代码的. zip 文件。相反，他们给了我一个 git 包文件，这是我从未听说过的(尽管我已经使用 git 很长时间了)。git 包文件本质上是单个文件中的完整存储库。你可以拥有分支、历史、标签，基本上你在一个存储库中所期望的一切，但是它们都包含在一个文件中。这使得共享存储库或者移动整个存储库变得非常简单。假设我有一个名为 myproj 的 git 项目，我想把它发送给其他人。

```
host1$ cd myproj
host1$ git bundle create repo.bundle master 
```

Enter fullscreen mode Exit fullscreen mode

这将生成一个包含主分支所有历史的包文件(repo.bundle)。然后你可以把这个文件发送给朋友，他们可以克隆它，就像它是一个远程 git 源一样:

```
host2$ git clone -b master ./repo.bundle myproj
host2$ cd myproj
host2$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean 
```

Enter fullscreen mode Exit fullscreen mode

关于 git 包的另一个非常好的事情是，如果您需要更新一个包，您可以发送一个只包含最新更改的增量文件更新。接收方只需将其放在现有捆绑包的位置，并在回购中运行`git pull`。

这不是你每天都会用到的东西，但是在某些情况下(比如把一个 git repo 从一个地方拿到另一个地方，而不需要共享一个远程源),它非常好用！

关于 git 包的更多信息:[https://git-scm.com/docs/git-bundle](https://git-scm.com/docs/git-bundle)