# 关于 Git 和 Vim 的一个简单(但强大)的技巧

> 原文：<https://dev.to/jovica/a-simple-yet-powerful-tip-on-git-and-vim-pam>

这里有一个提示，如果你使用 Vim 和`git`一起工作，你可能会觉得有用。最近想出来的。

例如，我想在不同的分支中查看一个文件，同时留在我当前的分支中(所以不改变分支)。我可以使用如下命令:

```
git show branch_name:/path/to/file.pl 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我想在 Vim 中看到这个文件呢？这很简单，我可以运行如下命令:

```
git show branch_name:/path/to/file.pl | vim - 
```

Enter fullscreen mode Exit fullscreen mode

然而，这种方式没有语法亮点，所以我们可以解决这个问题:

```
git show branch_name:/path/to/file.pl | vim - -c 'set syntax=perl' 
```

Enter fullscreen mode Exit fullscreen mode

很好:)现在我的 Perl 文件被正确地突出显示了。

好吧，但是如果我使用 Perl、Python 和 Bash 等呢？每次都键入所有这些内容不是很方便。

这里有一个解决方案——你可以创建别名:

```
alias vim.py="vim - -c 'set syntax=python'"
alias vim.pl="vim - -c 'set syntax=perl'" 
```

Enter fullscreen mode Exit fullscreen mode

所以下次你需要在不同于当前分支的分支中查看 Python 文件时，你可以运行:

```
git show branch_name:/path/to/file.py | vim.py 
```

Enter fullscreen mode Exit fullscreen mode

我希望你觉得这是有用的。

* * *

我在 http://masteringvim.com 的[快速掌握 Vim 简讯中分享了这样的技巧。](http://masteringvim.com)