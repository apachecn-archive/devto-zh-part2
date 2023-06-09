# 编写有意义的提交消息

> 原文：<https://dev.to/kewah/writing-a-meaningful-commit-message-57j0>

*照片由 [@kaitlynbaker](https://unsplash.com/@kaitlynbaker) 在[Unsplash](https://unsplash.com)T5 拍摄*

当在一个开发团队中工作时，分享你用代码实现了什么以及你为什么这样做是很重要的。可以使用明确的变量名、注释……也可以通过 Git 提交消息来完成。

提交消息适用于:

*   代码审查:它有助于审查者跟踪你的思路，理解你在你的 PR 中解决了什么。
*   它给代码行/代码块带来了上下文。
*   `git log`、`git rebase`、`git revert`或`git bisect`:它可以帮助你理解变化是什么，而不必深究提交中包含的每一行代码。

## 提交消息的结构

我遵循 Chris Beams 著名的[文章](https://chris.beams.io/posts/git-commit/#seven-rules)中详述的规则。它们是我在工作或开源项目中遇到的最常见的。

1.  [用空行将主体与主体分开](https://chris.beams.io/posts/git-commit/#separate)
2.  [将主题行限制在 50 个字符以内](https://chris.beams.io/posts/git-commit/#limit-50)
3.  [大写主题行](https://chris.beams.io/posts/git-commit/#capitalize)
4.  [不要以句号](https://chris.beams.io/posts/git-commit/#end)结束主题行
5.  [在主题行中使用祈使语气](https://chris.beams.io/posts/git-commit/#imperative)
6.  [在 72 个字符处换行](https://chris.beams.io/posts/git-commit/#wrap-72)
7.  [用身体解释*什么*和*为什么*vs*如何*](https://chris.beams.io/posts/git-commit/#why-not-how)

我喜欢按照他的方法写主题。在写留言的时候，我想到了下面这句话:

```
If applied, this commit will [your subject line] 
```

我强烈推荐你阅读完整的文章[如何编写 Git 提交消息](https://chris.beams.io/posts/git-commit)，它有很多很好的见解和指导。

## 当事情不按计划进行时

### git commit -am“一堆东西”

尽可能频繁地提交是一个好习惯，因为它简化了编写提交消息的工作，因此有一个更清晰的 Git 历史。但是，有时事情并不像计划的那样发展，我们做了太多的改变却没有承诺。希望可以使用`git add --path`分别存放和提交代码行。你可以在[Git Add Patch Command](http://codefoster.com/addpatch/)的文章中读到更多相关内容，作者在文章中描述了如何使用命令行或在 VSCode 中实现它。我更喜欢使用 GUI 来完成这项工作，因为我可以更好地了解我想要存放的内容，而不是使用 CLI 逐块进行。这实际上是我为 Git 打开 GUI 的唯一原因。

### git commit -am“哎呀忘了分号。”

由于某种原因 <sup>[*](#footnote1)</sup> ，一个错误通过 linter 传递并被提交。我们后来意识到，当 CI 失败时，PR 拒绝说我们忘记了分号。真扫兴。单独提交对评审者来说没有任何价值，如果我们需要恢复到以前的状态，我们可能不得不重做修复。幸运的是，PR 仍然是开放的，所以我们可以在正确的提交中添加丢失的分号。

如果错误发生在最后一次提交中，我们可以使用`--amend`标志编写修复、暂存和提交。这样，更改是之前提交的一部分，不公开。

```
git add -A
git commit --amend --no-edit
# Push the fix to the PR branch
git push origin --force-with-lease 
```

*你可以[阅读更多关于](https://developer.atlassian.com/blog/2015/04/force-with-lease/)为什么你应该用`--force-with-lease`而不是`--force`的内容。*

否则，如果这个问题发生在 Git 历史的早期，我们需要使用强大的 [`git rebase`](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase) 回到过去。首先，我们需要找到它发生在哪里，我们有几个选择:

*   使用`git blame`检查问题所在行的历史记录。
*   使用`git log --oneline`或`git log -p`(或使用您最喜欢的 GUI)检查日志以通读更改。
*   如果都不行，我们可以用 [`git bisect`](https://robots.thoughtbot.com/git-bisect) 。

根据我的经验，因为它与我的一个提交相关，所以我可以使用日志找到它。

我们现在已经找到了提交，我们可以使用它的 SHA 散列并对它进行 rebase:

```
git rebase -i e39a40e39bc325cde31b71402a79afb7be7e31ef^ 
```

*   `e38a...`是沙哈希
*   SHA 散列末尾的`^`指定我们希望从这个提交开始 rebase。如果没有它，它将在下一次提交时开始。

Git 打开一个新窗口 <sup>[**](#footnote2)</sup> ，在这里我们指定每次提交要运行的命令。这里，我们将第一行
中的`pick`替换为`edit`(或简称为`e`)

```
edit e39a40e The commit we want to update
pick 84da00a Another commit message

# Rebase e39a40e..84da00a onto e39a40e (2 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# d, drop <commit> = remove commit 
```

保存文件，Git 将我们带回提交。

现在，我们所要做的就是添加丢失的分号，进行更改，继续重定基础，最后将更改推送到远程分支。

```
git add -A
git rebase --continue
git push origin --force-with-lease 
```

*听说你喜欢用`-n`提交？👀

**可以[配置 Git](https://stackoverflow.com/a/2596835) 使用您最喜欢的编辑器。