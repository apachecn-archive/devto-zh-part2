# 一次杀死许多职业杀手

> 原文：<https://dev.to/dskuratovich/killing-many-procs-at-one-time-5dbe>

有时，我希望只用 BASH 控制台的一行代码就能终止许多进程。所有这些事情，比如从`top`或`ps`命令中找出 PID，手动杀死它们，让我很痛苦！

交易，它必须是一个外壳脚本，以便自动化它！
首先，我们要创建一个空文件来存放命令，让我们来做:

```
touch kill-all.sh 
```

Enter fullscreen mode Exit fullscreen mode

一旦文件被创建，我们必须使它可执行，很容易:

```
chmod +x ./kill-all.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个可以运行的脚本，我们需要用一些有用的指令来填充它:

```
#!/bin/bash

# Defining a proc name
proc_name=$1

# Clearing stdout
clear

# Performing killing
ps -aux | grep "$proc_name" | grep -v grep | cut -c 10-15 | xargs sudo kill -9 
```

Enter fullscreen mode Exit fullscreen mode

搞定了！现在，如果你要终止许多同名的进程，你只需要运行这个非常简单的指令:

```
./kill-all.sh slack 
```

Enter fullscreen mode Exit fullscreen mode

这将导致您关闭所有名为`slack`的进程

> 感谢阅读这篇文章！您可以关注我，以便从我这里获得最新的文章。
> 
> 像往常一样，如果你有任何问题，请在评论中告诉我！