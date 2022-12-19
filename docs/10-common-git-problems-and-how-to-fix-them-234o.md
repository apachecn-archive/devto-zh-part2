# 10 个常见的 Git 问题及其解决方法

> 原文：<https://dev.to/citizen428/10-common-git-problems-and-how-to-fix-them-234o>

我最初是在 2014 年 10 月为[共同导师](https://www.codementor.io/citizen428/git-tutorial-10-common-git-problems-and-how-to-fix-them-aajv0katd)写的这篇文章。从相当新的 git 用户到有经验的开发人员，它应该有适合每个人的东西。

## 1。放弃本地文件修改

有时候，了解问题的最佳方式是一头扎进去，摆弄代码。不幸的是，在此过程中所做的更改有时并不是最佳的，在这种情况下，将文件恢复到其原始状态可能是最快、最简单的解决方案:

```
git checkout -- Gemfile # reset specified path 
git checkout -- lib bin # also works with multiple arguments 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道，双破折号(`--`)是命令行实用程序表示命令选项结束的常用方式。

## 2。撤消本地提交

唉，有时我们要花更长的时间才能意识到我们在错误的轨道上，到那时，一个或多个变更可能已经在本地提交了。这就是`git reset`派上用场的时候:

```
git reset HEAD~2        # undo last two commits, keep changes
git reset --hard HEAD~2 # undo last two commits, discard changes 
```

Enter fullscreen mode Exit fullscreen mode

小心使用`--hard`选项！它重置了你的工作树和索引，所以你所有的修改将永远丢失。

## 3。从 git 中删除一个文件，但不从文件系统中删除它

如果您在`git add`期间不小心，您可能会添加您不想提交的文件。但是，`git rm`会将它从您的临时区域和文件系统中删除，这可能不是您想要的。在这种情况下，请确保您只删除暂存版本，并将该文件添加到您的`.gitignore`中，以避免第二次犯同样的错误:

```
git reset filename          # or git remove --cached filename
echo filename >> .gitignore # add it to .gitignore to avoid re-adding it 
```

Enter fullscreen mode Exit fullscreen mode

## 4。编辑提交消息

打字错误时有发生，但幸运的是在提交消息的情况下，修复它们非常容易:

```
git commit --amend                  # start $EDITOR to edit the message
git commit --amend -m "New message" # set the new message directly 
```

Enter fullscreen mode Exit fullscreen mode

但这不是我能为你做的全部。你忘记添加文件了吗？只需添加它并修改之前的提交即可！

```
git add forgotten_file 
git commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

请记住，`--amend`实际上会创建一个新的提交来替换之前的提交，所以不要用它来修改已经被推到中央存储库的提交。如果您完全确定没有其他开发人员已经签出了以前的版本并在此基础上开发了他们自己的工作，那么这个规则可以有一个例外，在这种情况下，强制推送(`git push --force`)可能仍然是可以的。这里`--force`选项是必要的，因为树的历史被本地修改，这意味着推送将被远程服务器拒绝，因为不可能有快进合并。

## 5。推送前清理本地提交

虽然`--amend`非常有用，但是如果您想要改写的提交不是最后一个，它也没有帮助。在这种情况下，交互式 rebase 就派上了用场:

```
git rebase --interactive 
# if you didn't specify any tracking information for this branch 
# you will have to add upstream and remote branch information: 
git rebase --interactive origin branch 
```

Enter fullscreen mode Exit fullscreen mode

这将打开您已配置的编辑器，并为您呈现以下菜单:

```
pick 8a20121 Upgrade Ruby version to 2.1.3 
pick 22dcc45 Add some fancy library 
# Rebase fcb7d7c..22dcc45 onto fcb7d7c 
# 
# Commands: # p, pick = use commit 
# r, reword = use commit, but edit the commit message 
# e, edit = use commit, but stop for amending 
# s, squash = use commit, but meld into previous commit 
# f, fixup = like "squash", but discard this commit's log message 
# x, exec = run command (the rest of the line) using shell 
# 
# These lines can be re-ordered; they are executed from top to bottom. 
# 
# If you remove a line here THAT COMMIT WILL BE LOST. 
# 
# However, if you remove everything, the rebase will be aborted. 
#
# Note that empty commits are commented out 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，您会看到一个本地提交列表，后面是对可用命令的解释。只需选择您想要更新的提交，将`pick`更改为`reword`(或简称为`r`)，您将被带到一个新的视图，在这里您可以编辑消息。

然而，从上面的清单中可以看出，交互式 rebases 提供的不仅仅是简单的提交消息编辑:您可以通过从列表中删除提交来完全删除提交，以及编辑、重新排序和压缩提交。挤压允许您将几个提交合并为一个，这是我喜欢在将功能分支推送到远程之前对其进行的操作。没有更多的“添加遗忘的文件”和“修复打字错误”提交记录为永恒！

## 6。还原推送的提交

尽管在前面的技巧中演示了修复，但是错误的提交偶尔会进入中央存储库。这仍然不是绝望的理由，因为`git`提供了一种简单的方法来恢复单次或多次提交:

```
 git revert c761f5c              # reverts the commit with the specified id
 git revert HEAD^                # reverts the second to last commit
 git revert develop~4..develop~2 # reverts a whole range of commits 
```

Enter fullscreen mode Exit fullscreen mode

如果您不想创建额外的恢复提交，而只是将必要的更改应用到您的工作树，您可以使用`--no-commit` / `-n`选项。

```
# undo the last commit, but don't create a revert commit 
git revert -n HEAD 
```

Enter fullscreen mode Exit fullscreen mode

位于`man 1 git-revert`的手册页列出了更多选项，并提供了一些额外的示例。

## 7。避免重复的合并冲突

正如每个开发人员都知道的，修复合并冲突可能是乏味的，但是重复解决完全相同的冲突(例如，在长时间运行的特性分支中)是非常烦人的。如果你在过去遭受过这种痛苦，你会很高兴地了解到未被充分利用的重用录制分辨率功能。将其添加到您的全局配置中，以便在所有项目中启用:

```
git config --global rerere.enabled true 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以通过手动创建目录`.git/rr-cache`，在每个项目的基础上启用它。

这当然不是每个人都有的功能，但是对于需要它的人来说，它可以真正节省时间。想象一下，您的团队正在同时处理不同的功能分支。现在您想要将它们全部合并到一个可测试的预发布分支中。正如所料，有几个合并冲突，您解决了。不幸的是，结果发现其中一个分支还不存在，所以您决定再次取消合并。几天(或几周)后，当分支最终准备好时，您再次合并它，但是由于记录的决议，您将不必再次解决相同的合并冲突。

手册页(`man git-rerere`)提供了更多关于用例及命令(`git rerere status`、`git rerere diff`等)的信息。

## 8。在合并后找到中断某些东西的提交

在一个大的合并之后，追踪引入一个 bug 的提交可能是相当耗时的。幸运的是`git`以`git-bisect`的形式提供了一个很棒的二分搜索法设施。首先你必须执行初始设置:

```
 git bisect start         # starts the bisecting session
 git bisect bad           # marks the current revision as bad
 git bisect good revision # marks the last known good revision 
```

Enter fullscreen mode Exit fullscreen mode

在此之后，git 将自动检出一个介于已知“好”和“坏”版本之间的版本。现在，您可以再次运行您的规范，并相应地将提交标记为“好”或“坏”。

```
git bisect good # or git bisec bad 
```

Enter fullscreen mode Exit fullscreen mode

这个过程一直持续到引入 bug 的提交。

## 9。避免使用 git 挂钩的常见错误

有些错误会重复发生，但是通过在`git`工作流的定义阶段运行某些检查或清理任务，可以很容易地避免。这正是钩子设计的场景。要创建一个新的钩子，向`.git/hooks`添加一个可执行文件。脚本的名称必须与可用的钩子之一相对应，手册页(`man githooks`)中提供了完整的列表。您还可以通过创建一个 git 将在初始化新的存储库时使用的模板目录，来定义在所有项目中使用的全局钩子(有关更多信息，请参阅 man git-init)。下面是`~/.gitconfig`中的相关条目和一个示例模板目录:

```
[init]
    templatedir = ~/.git_template

  → tree .git_template
  .git_template
  └── hooks
      └── pre-commit 
```

Enter fullscreen mode Exit fullscreen mode

当您初始化一个新的存储库时，模板目录中的文件将被复制到项目的`.git`目录中的相应位置。

接下来是一个稍微有点做作的示例`commit-msg`钩子，它将确保每个提交消息引用一个类似于“`#123`”的标签号。

```
ruby
  #!/usr/bin/env ruby
  message = File.read(ARGV[0])

  unless message =~ /\s*#\d+/
    puts "[POLICY] Your message did not reference a ticket."
    exit 1
  end 
```

Enter fullscreen mode Exit fullscreen mode

## 10。当一切都失败的时候

到目前为止，我们已经讨论了很多如何解决使用`git`时常见的错误。他们中的大多数人都有足够简单的解决方案，但是有时人们不得不拿出大枪，重写整个分支的历史。一个常见的用例是删除提交给公共存储库的敏感数据(例如生产系统的登录凭证):

```
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch secrets.txt' \
  --prune-empty --tag-name-filter cat -- --all 
```

Enter fullscreen mode Exit fullscreen mode

这将从每个分支和标签中删除文件`secrets.txt`。它还将删除由于上述操作而为空的任何提交。请记住，这将重写您的项目的整个历史，这在分布式工作流中是非常具有破坏性的。此外，尽管有问题的文件现在已被删除，但它包含的凭据仍应被视为受到了威胁！

GitHub 有一个关于移除敏感数据的非常好的教程和`man git-filter-branch`有各种可用过滤器及其选项的所有细节。