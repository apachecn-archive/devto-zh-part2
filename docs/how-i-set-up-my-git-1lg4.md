# 我如何设置我的 Git

> 原文：<https://dev.to/sandordargo/how-i-set-up-my-git-1lg4>

这不是另一篇关于 Git 别名的文章。有人喜欢他们，有人不喜欢。我只用几个。对于命令，我用得最多，也是我永远不会忘记的。换句话说，我懒的一直写`status`和`commit`。这些化名一文不值。另一方面，我在终端中修改了我的提示，为我提供了许多关于我所在的存储库的当前状态的信息，并且我还对 git 命令使用了自动完成功能。

当我不在 git 存储库中时，我的提示不会改变。这是我的提示符的默认状态:

[![Not in a git repo](img/3c79ef4f638890fe59eccd782fc53e7a.png "Not in a git repo")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ma5cyLGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0dibii4bky4h3osprh0q.png)

让我们通过在这里发出`git init`命令来创建一个新的 git 存储库，看看会发生什么。正如你在下图中看到的，我们在主分支上，另外我们看到一个标签。这个标签表明我们仍然处于初始化阶段，存储库初始化还没有完成。

[![After a git init](img/cfedc38ff068db7aef0ea1716774d5a9.png "After a git init")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g4RwYp1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/niv5kg5t6tpnvg9vl9m9.png)

让我们创建一个新文件(即 *somefile.txt* )并提交它。现在我们的存储库处于干净的状态。我们将只能看到当前分支的名称。

因此，如果我在一个 git 存储库中，并且它有一个干净的状态，我将简单地看到当前分支的名称:

[![In a clean branch](img/cfc1034b5aec2bdd4b1ec87fe2ce06b2.png "In a clean branch")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Tfwlsrkt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qezt6p36tiyxpcn2gp9w.png)

现在，我将修改 *somefile.txt* ，让我们看看提示是如何变化的。分支名称后会出现一个星号。这意味着我有一个未分级的跟踪文件。

[![Having an unstaged file](img/ea2f39ae0fd0e7e93fb0a3886f6fd46a.png "Having an unstaged file")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TpVOQN74--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q37rrez4xeq5f34z2sn6.png)

让我们添加另一个文件( *newfile.txt* )并立即登台。我们的提示又变了，我们都有了`*`和`+`。正如你可能已经猜到的，`+`是为了表明我们有一个阶段性的文件。

[![Having a staged and an unstaged file](img/e9ba717fa817886a8655d36855c1a6e8.png "Having a staged and an unstaged file")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2fyN73Pu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/637s17r13w15gk726bvq.png)

现在让我们也暂存 *somefile.txt* ，所以我们只有暂存文件。正如我们所料，`*`消失了，我们只剩下了`+`。

[![Having only staged files](img/095e4f026005f145244dbecd1243ec21.png "Having only staged files")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g8A4j5E3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iub7vznayg161har3zrd.png)

为了完成我们的周期，再次进行干净的回购，我们必须承诺。我们的工作区又干净了。没有未转移或转移的文件。

[![In a clean branch again](img/fb0b0e131b42a15d8dd3a6eb46a7759b.png "In a clean branch again")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nzaUgAQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9nvvakyqqhvmfbzsit9b.png)

## 关键要点

总结一下，我的提示将总是显示检出的分支名称或提交散列。此外，如果我们在跟踪的文件上有任何未暂存的更改，它将显示一个`*`,如果我们有任何暂存的文件，它将显示`+`。

它还在 rebases 期间和解决合并冲突时为您提供了一些额外的信息。

这可以节省你发出大量的`git branch`或`git rev-parse --abbrev-ref HEAD`来查看你在哪个分支，此外，你不需要一些`git status`命令，你只要看看你的提示就知道你是否处于一个干净的状态，或者当你准备下一次提交时，你是否仍然需要存放一些文件。

## 热得够呛

我不想为此居功。这要归功于卡罗琳和莎拉，她们在 Udacity 上了关于 Git 的超级课程。几年前，我们开始在我的公司使用 git，过了一段时间，我意识到如果我只使用 push/pull/commit/diff/status 五元组，我会一直有麻烦，我既不喜欢 git，也不喜欢我的工作。

我报名参加了这个课程，更好地理解了许多概念。他们还提供了一个不错的风格指南和这些脚本。我将永远感激他们。

Linux 和 Mac 用户将需要保存[这个文件自动完成](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash)和[这个文件用于检查你的回购状态](https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh)，和[这个](https://www.udacity.com/api/nodes/3341718587/supplemental_media/bash-profile-course/download?_ga=1.37232743.672083044.1467344711)帮助你使用前两个文件设置你的提示。

如果你在 Windows 上或者你想要更多的细节，查看一下[课程](https://classroom.udacity.com/courses/ud775)，它是免费的！

*本文最初发表在[我的博客](http://sandordargo.com/blog/2018/10/17/how-i-configure-my-git)上。*