# 寻找 Rails 迁移

> 原文：<https://dev.to/michaelherold/finding-rails-migrations-1dhc>

[![Scrabble pieces arranged to spell S-E-A-R-C-H signifying the search for Rails migrations across branches.](../Images/230abde2c09e23fe30ca43b046d7910a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hKr6M29F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oi1xuee9oiz1x32pozhg.jpg)

有时，我发现自己跨越几个分支，帮助不同的队友讲述他们的故事。通常，这些分支机构使用 Rails 迁移来轻松管理支持工作所需的数据库更改。这意味着我最终要从许多不同的分支运行迁移。因为我是人，所以我经常忘记在切换分支之前回滚那些迁移。当破坏性迁移作为分支的一部分必须发生时，它使我处于这样一种情况，我有几个迁移需要在任何给定的时间回滚。此外，如果是一个长期运行的分支，它们可能会与存在于`master`分支上的迁移交错。

## 缺失铁轨迁移

当我想要跨分支跟踪数据库迁移时，我使用 [`rails db:migrate:status`](https://guides.rubyonrails.org/command_line.html#bin-rails) 来查看当前分支上存在的每个迁移的状态。然而，当我运行没有合并到`master`分支的迁移时，我得到的输出如下:

```
 up     20180301163041  Add chocolate chips to the cookies
   up     20180302132931  ********** NO FILE **********
   up     20180302161231  Remove the egg from the recipe
   up     20180306171402  Change flour to almond flour 
```

Enter fullscreen mode Exit fullscreen mode

我发现自己在想，“迁移 201803021132931 从何而来？这是我当前任务需要回滚的内容吗？”当这种情况发生时，我需要一种方法来找出丢失的迁移做了什么，以便跟踪当我切换上下文时我需要做什么工作。为了解决这个问题，我创建了一个小的 Git 扩展，叫做 [`git-find-migration`](https://github.com/michaelherold/dotfiles/blob/8b3b472be89487111ddc742921e93f7b79542855/bin/git-find-migration) 。

## 新建工作流

我现在跨分支机构跟踪这些迁移的工作流程如下:

1.  我转到 Git 上的`upstream/master`分支。这为我提供了生产中正在运行的内容的当前状态的上下文(注意，我实际上并没有签出`upstream/master`，但是我将`master`的本地副本与它同步)。
2.  然后，我运行`g fm -l`来列出任何已经运行的迁移，但是我在分支上遗漏了(注意，我使用了一个名为`g` 的[函数和别名为`git-find-migration`到 fm](https://github.com/michaelherold/dotfiles/blob/8b3b472be89487111ddc742921e93f7b79542855/zsh/functions/g) 的[来使其更容易键入)。](https://github.com/michaelherold/dotfiles/blob/8b3b472be89487111ddc742921e93f7b79542855/bin/git-find-migration)
3.  对于我错过的每个迁移，我复制时间戳，然后运行`g fm <timestamp>`来查看我的 Git 存储库中的文件名。
4.  一旦我知道了文件名，我就运行`git log --all -- db/migration/<migration_file>`来查看该文件的上下文。

这个工作流程让我可以轻松地做出处理迁移的决策。有时，迁移是一个已经从存储库中删除的旧迁移(我可以补充一下，这不是一个建议的策略！)而且还好。有时候，我应该回滚以继续我的工作。在这种情况下，我检查带有迁移的分支并运行一个`rails db:migrate:down VERSION=<timestamp>`来回滚 <sup id="fn-rollback-return">[1](#fn-rollback)</sup> 。

## 缺点

我仍然可以对这个工作流程做一些改进。例如，我目前将我的脚本限制为一次只搜索一个时间戳。这是因为我不想弄清楚如何编写可以找到多个的 Git 查询。当我有几个需要回滚的迁移时，这可能会加快这个过程。当我们进行多阶段的、向后兼容的、破坏性的变更时，这种情况经常发生。

尽管有这些缺点，新的工作流程对我来说还是不错的。如果听起来有趣，请随意下载脚本。您有管理跨多个分支机构迁移的技术吗？

帖子[寻找 Rails 迁移](https://michaeljherold.com/2018/08/13/finding-rails-migrations/)最早出现在[的 Michael Herold](https://michaeljherold.com) 上。

1.  您总是确保您的迁移可以回滚，不是吗？ [↩︎](#fn-rollback-return)