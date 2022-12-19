# Git Stash Pop 如何 Git Stash 你的工作[正确的方法]

> 原文：<https://dev.to/neshaz/how-to-git-stash-your-work-the-correct-way-cna>

想象一下，你正在做一个项目的一部分，它开始变得混乱。有一个紧急的 bug 需要您立即关注。是时候**保存**你的更改并切换分支了。*问题是，你不想做半途而废的工作。*解决的办法是`git stash`。

> 如果您需要快速切换上下文并处理其他事情，但是您正在进行代码更改，并且还没有准备好提交，那么隐藏是很方便的。由[位存储桶](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)

## 贮藏

假设您目前有一些局部修改。运行 [`git status`](https://kolosek.com/git-commands-tutorial-part2/) ，查看您当前的状态:

```
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#      modified:   index.html
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#      modified:   assets/stylesheets/styles.css 
```

Enter fullscreen mode Exit fullscreen mode

你需要解决那个紧急的问题。首先，您想要保存未完成的工作变更而不提交它们。这就是`git stash`成为救世主的原因:

```
$ git stash
Saved working directory and index state WIP on master:
  bb06da6 Modified the index page
HEAD is now at bb06da6 Modified the index page
(To restore them type "git stash apply") 
```

Enter fullscreen mode Exit fullscreen mode

您的**工作目录**现在是干净的，所有未提交的本地更改都已保存！此时，您可以自由地进行新的更改，[创建新的提交](https://kolosek.com/git-merge/)，[切换分支](https://kolosek.com/git-branches/)，并执行任何其他 Git 操作。

> 默认情况下，栈被标识为“WIP”——正在进行的工作，位于创建它们的分支和提交之上。

## 重新使用你的存货

Git stash 是一个临时仓库。当您准备好从您离开的地方继续时，您可以轻松地恢复保存的状态:`git stash pop`。

**弹出您的储存库会从您的储存库中移除更改，并重新应用上次保存的状态。**如果你想**也保存变更**，你可以用`git stash apply`来代替。

## 附加提示和技巧

你还可以用藏毒做一些其他的事情。我们来看看吧！

*   **保存藏匿点**
    用**消息保存藏匿点** : `$ git stash save <message>`。

    > 试着在你的风格中加入 [CSS-line high](https://kolosek.com/css-line-height/) 并加上一个漂亮的注释。

*   **隐藏未跟踪文件**
    这是保存**未跟踪文件** : `$ git stash -u`或`$ git stash --include-untracked`的唯一方法

*   **列出多个 stashes**
    当你`git stash`或者`git stash save`的时候，Git 会创建一个带有名称的 Git commit 对象，然后保存在你的 repo 中。您可以随时查看您制作的**藏物清单**！`$ git stash list`。

```
$ git stash list
stash@{0}: On master: Modified the index page
stash@{1}: WIP on master: bb06da6 Initial Commit 
```

Enter fullscreen mode Exit fullscreen mode

*   **部分隐藏**
    你可以选择**隐藏**仅仅一个文件，一组文件，或者文件内的单个改变:`$ git stash -p`或者`$ git stash --patch`。

    > RSpec 测试是 Ruby on Rails 项目中必不可少的，但是它们可能并不总是完整的。只隐藏准备好的部分！

*   **查看隐藏差异**
    查看隐藏有两种方式:查看一个隐藏的**全部差异**-`$ git stash show -p`或者只查看最新的**隐藏** - `$ git stash show`。

```
$ git stash show
index.html | 1 +
style.css | 2 ++
2 files changed, 3 insertions(+) 
```

Enter fullscreen mode Exit fullscreen mode

*   **从隐藏的**创建一个分支
    创建一个**新分支**来应用您隐藏的更改，然后将您隐藏的更改弹出到它上面:`$ git stash branch <branch_name> <stash_id>`。

    > 这是另一种在继续项目之前节省开支的方法。

*   **拿出你的私藏**
    T5】小心使用，也许很难恢复。唯一的恢复方法是，如果你在删除存储后没有关闭终端。
    如果不再需要某个**特定的隐藏**，可以用`$ git stash drop <stash_id>`删除。或者你可以用`$ git stash clear`从回购中**删除你所有的**库存。

希望这篇文章能帮助你更好地理解 stashing 是如何工作的。一定要测试出来！

本文原载于 [Kolosek 博客](https://kolosek.com/git-stash/?utm_source=dvt)。