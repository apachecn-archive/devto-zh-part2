# Git 现场指南

> 原文：<https://dev.to/lucasmazza/git-field-guide-422o>

Git 已经成为软件开发不可或缺的工具，不管你的团队使用哪种语言或框架。但是另一方面，它不是一个你可以从第一天就采用、学习和有效的工具——它需要时间和指导来掌握它，无论是在命令行中还是在大多数团队已经采用的集成和实践中。

我整理了一份指南，介绍我在日常工作中如何看待和使用 git，这份指南可以作为一个起点，既适用于尝试 grok 的新人，也适用于希望在工作中更有效地使用 git 的更有经验的专业人士。

该指南描述了使用 git 时要记住并努力争取的 [**值**](#values) ，日常 [**实践**](#practices) 和 [**配置**](#configuration) 建议，我相信这些建议对于您的本地设置是必须有的。

## 值

1.  Git 更像是一个记录和交流变更的工具，而不是存储它们。随着参与人员的数量或在代码库中花费时间的增长，您最终会发现自己阅读了更多关于什么(以及为什么)发生了变化的信息，而不是回复变化。
2.  git**不是微不足道的**。它所有的能力和用途可能比你想象的更复杂。谦虚地理解你可能没有你想象的那样完全掌握它，并相信它比你现在期望的更强大。
3.  失败通常是可恢复的。每当一些 git 操作不按您的预期进行时(从提交到错误的分支到发生荒谬冲突的 rebases ),不要惊慌，因为并不是所有操作都被破坏或丢失，像`reflog`、`reset`或`rebase`这样的命令对于恢复和修复 git 树可能发生的任何事情都非常有用。哦，妈的，饭桶！是一个很小但很有用的资源，关于可能的 git 错误以及如何修复它们。

## 做法

*   **关于提交消息**:关心编写好的提交消息，并经常阅读您的 git 历史。Chris Beams 可能是[关于如何编写提交消息](https://chris.beams.io/posts/git-commit/)的最好的帖子，我建议阅读它，而不是试图在这个帖子上解释。
*   **当使用特性分支**时，总是保持它们是最新的，不时地使用`git pull`和`git fetch`，尽早并经常使用它的基础分支(大多数情况下是远程存储库的`master`分支)，并在冲突发生时尽快修复。在更大的 rebases 上修复冲突将会更加困难，合并将会在你的分支历史中增加嘈杂的提交。

```
git checkout feature/add-new-stuff
# work work work...
# download the refs that are on your remote repository, and rebase your feature
# branch on top of whatever's is on your remote's master branch, so it will be
# up to date. Hopefully no conflicts will need to be fixed.
git fetch origin && \
  git rebase origin/master

# if you want to make your local `master` branch is up to date as well, do
# the following: `git pull` will fetch and rebase the references from your
# remote repository, and then rebase the feature branch against the local
# master branch instead.
git checkout master && \
  git pull --rebase && \
  git checkout - && \
  git rebase master 
```

Enter fullscreen mode Exit fullscreen mode

*   **使用[git commit–fixup](https://robots.thoughtbot.com/autosquashing-git-commits)T3】和[交互式 rebases](https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history) 来重写特性分支的历史，以保持其提交有序且有组织。当滚动变更和尝试修复时，中间变更是有用的，但是一旦尘埃落定，您可以重做历史来表示您想要引入的最终变更。**

```
# So, you added a few files and some commits on your branch
git commit -m 'Add README.md'
# [master (root-commit) f1a4ab4] Add README.md
# 1 file changed, 0 insertions(+), 0 deletions(-)
# create mode 100644 README.md
$ git commit -m 'Add CONTRIBUTING.md'
# [master e8644e4] Add CONTRIBUTING.md
# 1 file changed, 0 insertions(+), 0 deletions(-)
# create mode 100644 CONTRIBUTING.md

# Now you need to update `README.md` and want to "merge" the new commit with
# these new changes to the original commit (f1a4ab4) using an interactive rebase
git add README.md
git commit --fixup f1a4ab4
# [master 13168c2] fixup! Add README.md
# 1 file changed, 0 insertions(+), 0 deletions(-)

# `--autosquash` will prepare the interactive rebase to squash 13168c2 on f1a4ab4
# for you.
git rebase --interactive --autosquash HEAD~3 
```

Enter fullscreen mode Exit fullscreen mode

*   在进行交互式再基础时，并不是所有的分支/拉请求都需要**压缩到一个提交**中。压缩太多的提交可能会使平分或浏览历史变得比您想要的更困难。你可以通过避免像 GitHub [“挤压合并”](https://github.com/blog/2141-squash-your-commits)按钮这样的快捷方式来实践这一点，并通过你的编辑器来支持 rebases，在那里你可以对操作有更多的控制，并审查你的提交来决定它们应该如何被挤压。

## 配置

*   将 git 配置为在使用`git config --global pull.rebase true`进行`git pull`时总是使用`rebase`分支，在使用`git config --global merge.ff false`进行合并时从不使用快进。在使用远程存储库时，这些更改对于维护合理的树历史至关重要。
*   在您使用的每个工作站上使用`git config --global user.name`和`git config --global user.email`配置您的身份。这有助于确定谁是变更的真正作者，对于 GitHub 这样的工具来说，将提交与特定的人相关联是很重要的。我还根据上下文用不同的电子邮件配置了我的本地存储库——提交给公司的相关工作使用商业电子邮件，而提交开源使用我的个人电子邮件——我有一个别名`git work`,它将“工作”身份设置为当前存储库，而我的全局配置仍然是个人的。
*   设置别名来抽象常见的 git 操作，例如:
    *   撤消最后一次提交(`git reset --soft HEAD^`)，并保留其更改以供审查或在不同的提交中进行拆分。
    *   向最新提交添加新的更改，同时保持相同的消息(`git commit --amend --no-edit`)。在应用诸如打字错误或样式改变之类的小修正时很有用，而不必做一个完整的`rebase`操作——不要忘记，当使用远程存储库时，你将需要使用`--force`来推送它。
    *   运行一个“自动的”交互式 rebase 来应用您的`--fixup`标记的提交，而不需要通过您的编辑器来描述 rebase 应该对每个提交(`!GIT_SEQUENCE_EDITOR=touch git rebase --interactive --autosquash`)做什么。

*更多我使用(和不使用)的配置设置和别名，可以在 GitHub 上查看我的 [gitconfig。](https://github.com/lucasmazza/dotfiles/blob/master/symlinks/gitconfig)*