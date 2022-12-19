# 在 Git 中的两个分支之间快速切换

> 原文：<https://dev.to/clairecodes/quickly-switching-between-two-branches-in-git-37k0>

> 签出您正在处理的最后一个分支。

您是否发现自己需要在 git 中的两个分支之间反复切换？在终端里有一条捷径！

不要每次都手动输入分支名称，而是用连字符`-`代替名称。

[![Gif demonstrating how to use the `git checkout -` shortcut](../Images/550db54ad3f66e88c95ac63b7d3b7eda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YRqx6GgX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rk5y0ckpj6u9zkcckval.gif)

*注意*:在 gif 中，我在 Mac 上使用 iTerm2 中的 Bash shell。

如果您看不到 gif，这里有一个文本表示，其中带有美元的行`$`表示命令提示符:

```
[~test-directory] | first-really-long-branch-name $ git checkout second-super-long-branch-name
Switched to branch 'second-super-long-branch-name'

[~/test-directory] | second-super-long-branch-name $ git checkout -
Switched to branch 'first-really-long-branch-name'

[~/test-directory] | first-really-long-branch-name $ git checkout -
Switched to branch 'second-super-long-branch-name'

[~/test-directory] | second-super-long-branch-name $ 
```

Enter fullscreen mode Exit fullscreen mode

在两个分支之间切换时，此快捷方式可以节省击键次数。

当您处理具有长名称的分支时，或者您的分支具有相似的名称时，它会很有帮助，这使得自动完成功能没有发挥应有的作用(例如，`feature-1123`、`feature-1124`)。

* * *

### P.S

说到 git 中的 autocomplete，[这里是如何为 Bash shell](https://git-scm.com/book/en/v1/Git-Basics-Tips-and-Tricks) 安装它，如果你还没有安装它的话。这提供了键入几个字符并按 tab 键自动完成命令的其余部分的能力(或者如果有多个匹配，则查看所有可能性)。

* * *

### P.P.S

同样的技巧可以用于在终端中的两个目录之间导航。尝试在命令提示符下键入`cd -`，当前目录将会变成您上一次所在的目录:

```
[~/test-directory] | master $ cd /a/long/subdirectory/path

[~/test-directory/a/long/subdirectory/path] | master $ cd -

[~/test-directory] | master $ 
```

Enter fullscreen mode Exit fullscreen mode