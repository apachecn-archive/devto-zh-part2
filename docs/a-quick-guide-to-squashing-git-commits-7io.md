# 粉碎 Git 提交的快速指南

> 原文：<https://dev.to/victoria/a-quick-guide-to-squashing-git-commits-7io>

让我们先弄清楚一件事:仅仅为了拥有一棵漂亮的树而重写 Git 历史，特别是对于公共存储库，通常是不可取的。这有点像回到过去，你对你的项目版本所做的改变导致它看起来与别人从你现在已经抹去的历史点上分出来的版本完全不同——我的意思是，你没看过*回到未来第二部分*吗？(如果你宁愿坚持只拍了一部*回到未来*的电影，这样你未来的自己就不用看续集了，我明白。)

这里是要点。如果您已经将混乱的提交推送到一个公共存储库，我建议您直接去做，不要让事情变得更复杂。(我们都从自己的尴尬中学习，尤其是公共场合——我看着你，过去——维多利亚。)如果你的乱七八糟的提交目前只存在于你的本地版本上，那太好了！我们可以把它们整理成一个干净的、描述良好的承诺，我们将自豪地推动它，没有人会知道。

有几种不同的方式来粉碎提交，选择合适的方式取决于我们需要实现的目标。

以下示例使用`git log --graph`进行说明，为了简洁起见，还提供了一些选项。我们可以设置一个方便的别名，在我们的终端中使用
来查看这个日志格式

```
git config --global alias.plog "log --graph --pretty=format:'%h -%d %s %n' --abbrev-commit --date=relative --branches" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们就做`git plog`来看漂亮的圆木。

# 方法#1:一次提交以统治主分支

这适用于以下情况:

*   我们直接向师父承诺
*   我们不打算打开一个拉请求来合并一个特征
*   我们不想保留我们还没有推动的分支或变更的历史

这个方法采用一个 Git 树，看起来像这样:

```
*   3e8fd79 - (HEAD ->  master) Fix a thing
|
*   4f0d387 - Tweak something
|
*   0a6b8b3 - Merge branch 'new-article'
|\
| * 33b5509 - (new-article) Update article again again
| |
| * 1782e63 - Update article again
| |
| * 3c5b6a8 - Update article
| |
* | f790737 - (master) Tweak unrelated article
|/
|
* 65af7e7 Add social media link
|
* 0e3fa32 (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

并使它看起来像这样:

```
* 7f9a127 - (HEAD ->  master) Add new article
|
* 0e3fa32 - (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

这是怎么做的——抓住你的悬浮滑板，这超级复杂

```
git reset --soft origin/master
git commit 
```

Enter fullscreen mode Exit fullscreen mode

是的，就这些。我们可以用`git branch -D new-article`删除不需要的分支。

# 方法二:没有*那种*厉害！

这适用于以下情况:

*   我们想要压缩最后的 *x* 提交，而不是从`origin/master`开始的*所有*提交
*   我们想要打开一个拉请求来合并一个分支

这个方法采用一个 Git 树，看起来像这样:

```
* 13a070f - (HEAD ->  new-article) Finish new article
|
* 78e728a - Edit article draft
|
* d62603c - Add example
|
* 1aeb20e - Update draft
|
* 5a8442a - Add new article draft
|
| * 65af7e7 - (master) Add social media link
|/
|
* 0e3fa32 - (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

并使它看起来像这样:

```
* 90da69a - (HEAD ->  new-article) Add new article
|
| * 65af7e7 - (master) Add social media link
|/
|
* 0e3fa32 - (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

为了将分支`new-article`上的最后五个提交压缩成一个，我们使用:

```
git reset --soft HEAD~5
git commit -m "New message for the combined commit" 
```

Enter fullscreen mode Exit fullscreen mode

其中,`--soft`保持我们的文件不变并被暂存，而`5`可以被认为是“我想要组合的先前提交的数量”

然后我们可以做`git merge master`并创建我们的拉取请求。

# 方法三:变得挑剔

假设我们度过了一个非常混乱的下午，我们的 Git 树看起来像这样:

```
* dc89918 - (HEAD ->  master) Add link
|
* 9b6780f - Update image asset
|
* 6379956 - Fix CSS bug
|
*   16ee1f3 - Merge master into branch
|\
| |
| * ccec365 - Update list page
| |
* | 033dee7 - Fix typo
| |
* | 90da69a - Add new article
|/
|
* 0e3fa32 - (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

我们希望保留一些历史，但是清理提交。我们还想更改一些提交的消息。为此，我们将使用`git rebase`。

这适用于以下情况:

*   我们只想粉碎一些提交
*   我们希望编辑以前的提交消息
*   我们希望删除或重新排序特定的提交

Git `rebase`是一个强大的工具，一旦我们掌握了它，就很方便。为了改变自`origin/master`以来的所有提交，我们做:

```
git rebase -i origin/master 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以做:

```
git rebase -i 0e3fa32 
```

Enter fullscreen mode Exit fullscreen mode

其中，提交散列是我们想要保持原样的最后一次提交。

`-i`选项让我们运行交互式 rebase 工具，它启动我们的编辑器，本质上是一个供我们修改的脚本。我们将看到与 git 日志顺序相反的提交列表，最旧的在最上面:

```
pick 90da69a Add new article
pick 033dee7 Fix typo
pick ccec365 Update list page
pick 6379956 Fix CSS bug
pick 9b6780f Update image asset
pick dc89918 Add link #  Rebase 0e3fa32..dc89918 onto 0e3fa32 (6 commands)
#  #  Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message #  x, exec = run command (the rest of the line) using shell #  d, drop = remove commit #  #  These lines can be re-ordered; they are executed from top to bottom. #  #  If you remove a line here THAT COMMIT WILL BE LOST. #  #  However, if you remove everything, the rebase will be aborted. #  #  Note that empty commits are commented out #  ~ 
```

Enter fullscreen mode Exit fullscreen mode

这些评论给了我们一个方便的指南，告诉我们能做什么。现在，让我们把有小变化的提交压缩成更重要的提交。在我们的编辑器中，我们将脚本修改成这样:

```
pick 90da69a Add new article
squash 033dee7 Fix typo
pick ccec365 Update list page
squash 6379956 Fix CSS bug
squash 9b6780f Update image asset
squash dc89918 Add link 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们保存了更改，交互式工具将继续运行。它会按顺序执行我们的指令。在这种情况下，我们再次看到编辑器显示如下:

```
#  This is a combination of 2 commits.
#  This is the 1st commit message:
 Add new article #  This is the commit message #2:
 Fix typo #  Please enter the commit message for your changes. Lines starting
#  with '#' will be ignored, and an empty message aborts the commit.
#  #  interactive rebase in progress; onto 0e3fa32
#  Last commands done (2 commands done):
#  pick 90da69a Add new article
#  squash 033dee7 Fix typo
#  Next commands to do (4 remaining commands):
#  pick ccec365 Update list page
#  squash 6379956 Fix CSS bug
#  You are currently rebasing branch 'master' on '0e3fa32'.
#  #  Changes to be committed:
#  modified:   ...
#  ~ 
```

Enter fullscreen mode Exit fullscreen mode

如果我们愿意，这是我们为第一次挤压创建新的提交消息的机会。一旦我们保存它，交互式工具将继续下一个指令。除非...

```
[detached HEAD 3cbad01] Add new article
 1 file changed, 129 insertions(+), 19 deletions(-)
Auto-merging content/dir/file.md
CONFLICT (content): Merge conflict in content/dir/file.md
error: could not apply ccec365... Update list page

Resolve all conflicts manually, mark them as resolved with "git add/rm <conflicted_files>", then run "git rebase --continue". You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".

Could not apply ccec365... Update list page 
```

Enter fullscreen mode Exit fullscreen mode

同样，该工具提供了一些非常有用的说明。一旦我们解决了合并冲突，我们就可以用`git rebase --continue`继续这个过程。我们的交互式 rebase 从它停止的地方开始。

一旦所有的压缩都完成了，我们的 Git 树看起来就像这样:

```
* 3564b8c - (HEAD ->  master) Update list page
|
* 3cbad01 - Add new article
|
* 0e3fa32 - (origin/master, origin/HEAD) Update theme 
```

Enter fullscreen mode Exit fullscreen mode

唷，好多了。

*这篇文章是从我博客上一篇关于 Git 提交实践的长文章[中摘下来的。](https://victoria.dev/verbose/git-commit-practices-your-future-self-will-thank-you-for/)T3】*