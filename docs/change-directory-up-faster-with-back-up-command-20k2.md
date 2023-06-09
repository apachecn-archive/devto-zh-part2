# 使用备份命令更快地更改目录

> 原文：<https://dev.to/tmidi/change-directory-up-faster-with-back-up-command-20k2>

首先发布在[midiroot.com](https://midiroot.com)。

[多诺万·布朗](http://donovanbrown.com)写了一篇很好的[文章](http://donovanbrown.com/post/Why-cd-when-you-can-just-backup)，展示了如何使用定制的 PowerShell 函数向上导航目录，而不需要键入多个`cd ..`。我觉得他的想法很有趣，如果你花很多时间在 PowerShell 上，可能会节省很多时间。

不幸的是(或者幸运的是，这取决于你如何看待它)现在我不怎么使用 PowerShell，当我使用 PowerShell 时，我的使用仅限于几个 PowerCLI 命令，但是我确实花了相当多的时间与 Linux Shell 交互，所以自然要做的事情是将 Donovan 的想法移植到 Bash 脚本中。Bash 脚本将不同于 PowerShell 脚本，但是概念是相同的。

我们需要什么？

*   以整数作为自变量函数。
*   当输入错误参数时显示基本帮助菜单的帮助功能。

## 帮助功能:

我想尽可能保持简单，因为我们的函数只需要一行参数，第二行表示这是一个帮助菜单:

```
 function bu_usage () {
       cat <<-EOF Usage: bu [N]
                N        Where N is the number of level to move back to. this argument must be an integer.
                h help   displays this basic help menu. EOF    } 
```

Enter fullscreen mode Exit fullscreen mode

我使用了`-EOF`来允许缩进，这对于制表符比对于空格效果更好。

## 备份功能

现在我们的帮助功能已经完成，我们可以开始构建备份功能了。我们知道函数将接受一个参数，这个参数必须是一个整数:

```
function bu () {
    FUNCTIONARG=$1
    # Make sure the provided argument is a positive integer:
    if [[ ! -z "${FUNCTIONARG##*[!0-9]*}" ]]; then
        for i in $(seq 1 $FUNCTIONARG); do STRARGMNT+="../"
        done CMD="cd ${STRARGMNT}"
        eval $CMD
    else bu_usage
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

**这是如何工作的？**函数首先确保参数是一个有效的整数，如果不是，它将调用我们之前创建的帮助函数。然后使用从 1 到参数的数字序列，我们将在每次迭代中把`../`字符串附加到`STRARGMNT`上。当 for 循环完成时，我们使用所有最终追加的目录运行`cd`命令。这将为我们提供:

| 争吵 | 命令 |
| --- | --- |
| one | 激光唱片.. |
| Two | 激光唱片../.. |
| three | 激光唱片../../.. |

## 这个怎么用？

我通常会将类似这样的函数添加到主目录下的一个`.functions`文件中，函数文件通过`.bash_profile`获取源代码。
到源`.functions`或其他点文件添加这个 for 循环到你的`.bash_profile`，文件必须用逗号分隔:

```
for file in ~/.{functions}; do
    [ -r "$file" \] && \[ -f "$file" \] && source "$file";
done;
unset file; 
```

Enter fullscreen mode Exit fullscreen mode

创建`.functions`并将此内容添加到其中:

```
 function bu () {
    function bu_usage () {
       cat <<-EOF Usage: bu [N]
                N        Where N is the number of level to move back to. this argument must be an integer.
                h help   displays this basic help menu. EOF    }

    # unset variables
    STRARGMNT=""
    FUNCTIONARG=$1

    # Make sure the provided argument is a positive integer:
    if [[ ! -z "${FUNCTIONARG##*[!0-9]*}" ]]; then
        for i in $(seq 1 $FUNCTIONARG); do STRARGMNT+="../"
        done CMD="cd ${STRARGMNT}"
        eval $CMD
    else bu_usage
    fi
} 
```

Enter fullscreen mode Exit fullscreen mode

这与我们之前创建的函数略有不同。帮助菜单是主`bu`功能内的嵌套功能。我们还创建了空的`STRARGMNT`来在每次函数运行时取消变量的设置。

当您完成`source .bash_profile`或关闭并重新打开终端以使更改生效时

**怎么跑？**
`bu help`或`bu -h`为帮助菜单。
`bu 2`向上移动两个文件夹，这相当于运行`cd ..\..\`
你可以在这里看到`bu`的动作(外部链接):

[![asciicast](img/3cd6734bbd5fd536d1234c428930941b.png)T2】](https://asciinema.org/a/Serx0ac08heiRW4QI61FW2QKv)