# git 是如何出错的

> 原文：<https://dev.to/adityasridhar/how-you-can-go-wrong-with-git-3lkn>

**原载于[adityasridhar.com](https://adityasridhar.com/posts/how-you-can-go-wrong-with-git)T3】**

我无法提交到远程存储库，让我执行强制推送。

让我在远程存储库上运行 Rebase，使提交历史更加整洁。

让我修改一下远程存储库中我之前的提交。

上面提到的几点是在 Git 中应该避免做的事情。

在我之前的帖子中，我已经讲述了关于 [git 基础知识](https://adityasridhar.com/posts/what-is-git-and-how-to-use-it)和 [git 修正和重置](https://adityasridhar.com/posts/how-to-become-a-git-expert)。点击链接了解更多信息。

Git 有惊人的特性，对开发者很有帮助。但是在使用 git 的时候还是会出错。在这里，我将提到使用 git 时应该避免的一些事情，并解释为什么应该避免它们。

# 强制推送到远程存储库

<figure>

[![Force Push](img/5155206687b6b51e46bcc3e0e40bc410.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rtfRl87Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5t4vq13p9xgt701qvl3s.jpg)

<figcaption>Photo by [Tim Mossholder](https://unsplash.com/@timmossholder) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

1.  假设两个开发人员在一个分支上工作。
2.  开发人员 1 已经完成了更改，并将代码推送到远程存储库。
3.  现在 Developer 2 已经完成了更改，但是无法将代码推送到远程存储库。开发者 2 也是 Git 的初学者。
4.  开发人员 2 进行了快速的 google 搜索，发现了 force push 命令并使用了它。命令是`git push -f`
5.  开发人员 1 检查远程存储库，却发现开发人员 1 编写的代码已经完全消失了。

这是因为强制推送会覆盖远程存储库中的代码，因此远程存储库中的现有代码会丢失。

处理上述场景的理想方式是，开发人员 2 需要从远程存储库中提取最新的代码变更，并将代码变更重新存储到本地存储库中。一旦成功地完成了 rebase，开发人员 2 就可以将代码推送到远程存储库中。**这里我们讨论的是在同一个分行中从远程回购到本地回购的转基。**

除非绝对必要，否则避免强行推动。只有在别无选择的情况下才使用它。但是请记住，强制推送将覆盖远程存储库中的代码。

事实上如果你使用的是类似源码树的用户界面，默认情况下强制推送是禁用的。您必须手动启用它才能使用它。

同样，如果使用正确的 [git 工作流](https://adityasridhar.com/posts/how-to-use-git-efficiently)，每个开发人员将拥有他们自己的特性分支，这样的场景甚至不会发生。

# 尝试重置远程存储库

<figure>

[![Remote Rebase](img/0f6d184c07305397985f40cfc13449f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6e7GlMHz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gz3z0y2f8z24cbrp6vp7.jpg)

<figcaption>"green, red, and white high voltage circuit breaker" by [Ben Hershey](https://unsplash.com/@benhershey) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

1.  假设两个开发人员正在开发一个特性分支。
2.  开发人员 1 完成了一堆提交，并将其推送到远程特性分支。
3.  开发人员 2 将最新的变更从远程特性分支带入本地特性分支。
4.  开发人员 2 向本地特性分支添加了一堆提交。
5.  但是 Developer 2 也希望确保来自发布分支的最新变更被重新放入特性库中。因此，开发人员 2 将发布分支重新置于本地特性分支之上。**这是不同分行从远程到本地回购完成的转基**。
6.  现在，开发人员 2 试图将代码推送到远程特性分支。Git 不允许这样做，因为提交历史已经改变。所以开发者 2 会进行强制推送。
7.  现在，当开发人员 1 想要从远程特性分支获取最新的代码时，这是一项艰巨的工作，因为提交历史已经改变。因此，开发人员 1 需要处理大量的代码冲突(甚至一些冗余的代码冲突，开发人员 2 已经处理了)

对远程存储库进行重新设置将会改变提交历史，并且当其他开发人员试图从远程存储库获取最新代码时，将会产生问题。

处理这种情况的理想方式是总是只对本地存储库进行重新调整。本地存储库中的任何提交都不应被推送到远程存储库中。

如果任何提交已经被推送到远程特性分支，那么最好与发布分支进行合并，而不是重定基础，因为合并不会改变提交历史。

此外，如果使用正确的 git 工作流，只有一个人会在一个功能分支上工作，这个问题甚至不会发生。如果只有一个人在特性分支上工作，并且在远程特性分支上做了一个 rebase，那么就没有问题，因为没有其他开发人员从同一个远程特性分支中提取代码。但是最好避免重定远程存储库的基础。

Rebase 是一个非常强大的功能，但是要小心使用。

# 修改远程存储库中的提交

<figure>

[![Amending commirts in remote repository](img/51cb245a0df5294c92f9d9723f02bdf5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GHPpIfpW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mvxftfniai5plz7xqha0.jpg)

<figcaption>"broken ceramic plate on floor" by [chuttersnap](https://unsplash.com/@chuttersnap) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

1.  假设两个开发人员正在开发一个特性分支。
2.  开发人员 1 已完成提交并将其推送到远程功能分支。姑且称之为“犯老”。
3.  开发人员 2 将最新的代码从远程特性分支拉入本地特性分支
4.  开发人员 2 正在处理本地存储库中的代码，还没有将任何代码推送到远程存储库中。
5.  开发人员 1 意识到提交中存在错误，并在本地回购中修改提交。姑且称之为“提交新的”。
6.  开发人员 1 试图将修改后的提交推送到远程功能分支。但是 git 不允许这样做，因为提交历史发生了变化。所以开发者 1 进行了一次强制推送
7.  现在，当 Developer 2 想要从远程特性分支获取最新的代码时，git 会注意到提交历史中的差异，并创建一个合并提交。现在，当开发人员 2 查看本地回购中的提交历史时，开发人员 2 会注意到“提交旧的”和“提交新的”。这破坏了修改提交的全部意义。
8.  即使 Developer 2 从远程分支到本地分支做了一个 rebase，“旧提交”将仍然存在于 Developer 2 的本地分支中。因此它仍然是提交历史的一部分。

**修改提交会改变提交历史。因此，当其他开发人员试图从远程存储库中获取最新代码时，修改远程存储库中的提交将会造成混乱。**

最佳实践是只在本地存储库中修改提交。一旦提交到远程存储库中，最好不要做任何修改。另外，如果使用正确的 git 工作流，只有一个人会在一个功能分支上工作，这个问题甚至不会发生。在这种情况下，修改远程存储库不会产生任何问题，因为没有其他开发人员从相同的远程特性分支提取代码。

# 硬复位

<figure>

[![Hard reset](img/2a6efa29dff3765e559c2087177455c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--87tbMTD2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h15umwlormnigs6uoczw.jpg)

<figcaption>"clear hour glass beside pink flowers" by [Nathan Dumlao](https://unsplash.com/@nate_dumlao) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

1.  使用`git reset --hard`完成硬复位
2.  还有其他类型的复位，如`--soft`和`--mixed`，它们没有硬复位危险
3.  假设开发人员 1 正在处理一个特性分支，并且在本地 repo 中完成了 5 次提交。
4.  另外，开发人员 1 目前正在处理 2 个尚未提交的文件。
5.  如果开发者 1 运行`git reset --hard <commit4hash>`，将会发生以下事情。
6.  功能分支中的最新提交现在将是提交 4，提交 5 将丢失。
7.  开发人员 1 正在处理的 2 个未提交的文件也丢失了

Commit 5 仍然在 git 内部，但是对它的引用丢失了。我们可以使用`git reflog`取回 commit5。但尽管如此，使用硬复位仍然非常危险。

在 git 中使用重置命令时要非常小心。在某些情况下，您可能必须使用复位，但在进行硬复位之前，请对情况进行全面评估

# 如何知道使用 git 时有哪些不好的做法？

<figure>

[![How to know what are the bad practises while using git](img/68a45b80ca22d158a3ebdac126f3dc25.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--poe5kjAz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4lqyaltoyrn5oocidxxz.jpg)

<figcaption>"question mark neon signage" by [Emily Morter](https://unsplash.com/@emilymorter) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

我上面提到的列表并没有涵盖所有内容。它只是列出了一些在使用 git 时可能出错的地方。

那么，一般来说，您如何知道在使用 git 时应该小心些什么呢？

1.  在上面的列表中，您可能已经观察到了一个常见的事情，即当多个人在同一个分支工作时，问题就会发生。因此，使用正确的 git 工作流将确保一次只有一个人在一个特性分支上工作。发布分支将由技术主管或高级开发人员处理。此工作流程可以防止一些重大问题的发生。
2.  你会观察到的另一个常见现象是到处使用力推。默认情况下，Git 确保您不能在远程存储库中进行任何破坏性的更改。但是强制推送覆盖了 git 的默认行为。
3.  因此，无论何时，当你处于不得不用力推的位置时，只把它作为最后的手段。也要评估是否有其他不使用强迫的方法来达到你的目的。
4.  任何改变远程存储库中提交历史的操作都是危险的。仅在本地存储库中更改提交历史记录。但是即使在本地存储库中，在使用硬重置时也要小心。
5.  在非常小的项目中，使用 git 工作流可能有些矫枉过正。因此在这些项目中，多个开发人员将在同一个分支上工作。但是在对远程存储库进行任何重大更改之前，最好先评估一下这是否会影响其他开发人员。

**上面说的几点主要是针对有多个开发者的项目。如果你使用 git 作为你爱好项目的备份，那么应该没有问题。**

像强制推送和硬重置这样的事情是 git 的特性。它们在某些情况下非常有用。但是最好在使用它们之前进行评估

## 希望这篇文章能给你一些关于 git 中可能出错的地方以及如何避免的想法😄

### 欢迎在 [LinkedIn](https://www.linkedin.com/in/aditya1811/) 与我联系，或在 [twitter](https://twitter.com/adityasridhar18) 关注我。

### 如果你喜欢这个帖子，你可以去我的网站【https://adityasridhar.com T2】查看其他类似的帖子