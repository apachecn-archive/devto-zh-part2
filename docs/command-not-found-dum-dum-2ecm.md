# 找不到命令...达姆达姆

> 原文：<https://dev.to/rpalo/command-not-found-dum-dum-2ecm>

你知道现在几点了吗？没错。是时候了

## 快速提示

这个技巧来自第二版的 Bash 袖珍参考。阿诺德·罗宾斯。

你知道当你给 Bash(和类似的 shells)一个命令时，它是如何寻找命令的吗？列表如下:

1.  首先，它检查你输入的是否是一个语言关键字，比如`for`或`while`。

2.  接下来，它会检查您的别名。有趣的是，上面的书声明(并引用了其他同意的资料)你基本上不应该使用别名！它说编写一个函数几乎总是应该是首选的——与我看到的许多 StackOverflow 答案相反。实际上，我想我同意。编写函数看起来更干净，以后更容易返回和修改。

3.  然后，它检查特殊的内置函数，如`break`、`exit`或`export`。Bash 语言的内部不一定需要这些，但是脚本和交互式 shells 需要它们。

4.  之后，它会查看您定义的任何函数。

5.  接下来是非特殊内置。这些命令类似于`cd`和`test`。因为函数在这些之前被检查，你可以用你自己的函数覆盖`cd`!

```
function cd() {
  echo "You're the best!"
  command cd "$@"  # Actually calls the real `cd`
}

$ cd ~/code
# => You're the best!
# Now in ~/code 
```

最后，它搜索`$PATH`试图找到匹配的脚本。

提示如下:如果 Bash 在这些地方都找不到您输入的命令，它会运行一个名为`command_not_found_handle`的函数。如果你碰巧覆盖了这个函数，它会调用你的版本来代替*！*

所以，如果你的终端体验不够敌对，你可以可行地在你的`.bash_profile`中加入以下内容。

```
function command_not_found_handle() {
  options=(
    "no."
    "No!"
    "NO."
    "OMG NO."
    "Mother ****!"
    "WHAT ARE YOU DOING?"
    "Success!  JK, you're still a dum dum."
  )
  option_choice=$(( $RANDOM % 7 ))
  echo "${options[$option_choice]} '$*' command not found."
  # The command that you tried is passed into
  # this function as arguments, so $* will contain
  # the entire command + arguments and options

  return 127   # 127 is the canonical exit code for
                        # "command not found" 
} 
```

然后，当您打开终端时，您应该会看到以下内容:

```
$ hwaaaa
# => NO. 'hwaaaa' command not found.
$ but why not tho
# => Success!  JK, you're a dum dum.  'but why not tho' command not found. 
```

玩笑归玩笑，希望你开始明白如何编写一些脚本来提供稍微更有帮助/友好的错误消息，甚至可能显示一些与你输入的内容接近的可能选项？

**快翻翻。**