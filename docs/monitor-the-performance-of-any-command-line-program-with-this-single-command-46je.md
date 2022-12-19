# 用这条命令监控任何命令行程序的性能

> 原文：<https://dev.to/agrim/monitor-the-performance-of-any-command-line-program-with-this-single-command-46je>

这篇文章最初发表在我的博客上，在这里找到原文。

最近，我在 linux 命令行上执行了很多 bash 脚本和一次性进程，我很好奇如何尽快监控这些进程的 CPU 和内存使用情况。其中一些进程似乎让我的 linux 机器嘎然而止，如果没有即时资源监控，我无法判断这样的进程是否占用了过多的 RAM 或 CPU(或者两者都有？！)

## 人们通常做什么

按照惯例，[关于过程监控的建议](https://unix.stackexchange.com/questions/554/how-to-monitor-cpu-memory-usage-of-a-single-process)包括以下内容:

1.  运行您的命令行进程。

2.  用一般适用于 Linux 和 Mac 的 [`ps aux`](https://unix.stackexchange.com/questions/106847/what-does-aux-mean-in-ps-aux) 命令获取进程的 PID。

3.  运行 [`top`](http://man7.org/linux/man-pages/man1/top.1.html) 实用程序

    ```
    top -p <pid> 
    ```

    在 Linux 或

    ```
    top -pid <pid> 
    ```

    在 Mac OS 上。

然而，在我可以运行`top`开始监控它们之前，我想运行几秒钟内就会退出的命令。如何在这些命令被调用后立即开始监控它们？

## 我们能做得更好吗

和往常一样，Unix 有一个答案:

1.  在后台运行您的命令，并将输出传送到`/dev/null`,这样当您监视 CPU/内存使用时，它就不会在命令行上显示出来，例如

    ```
    unzip ~/Downloads/sample.zip > /dev/null & 
    ```

2.  回显此后台进程的 PID

    ```
    echo $! 
    ```

3.  将两者结合起来，并将流程的 pid 传递给`top`

    ```
    top -p `{ unzip ~/Downloads/sample.zip > /dev/null & } && echo $!` 
    ```

    在 linux 或

    ```
    top -pid `{ unzip ~/Downloads/sample.zip > /dev/null & } && echo $!` 
    ```

    在 Mac OS 上。

嘭，您只需一个命令就可以立即开始监控您的流程。这不是很好吗？

## 加成:用`htop`代替`top`

I❤️htop 是一个进程监控工具，适用于 linux 和 Mac OS。来吧，你有颜色！让我们用这个代替`top`来更好地了解我们进程的 CPU/内存性能。

1.  安装`htop`与

    ```
    sudo apt-get install htop 
    ```

    在 Ubuntu 或

    ```
    brew install htop 
    ```

    在装有[自制软件](https://brew.sh/)的 Mac OS 上

2.  运行我们之前的监控命令，使用`htop`而不是`top`进行监控

    ```
    htop -p `{ unzip ~/Downloads/sample.zip > /dev/null & } && echo $!` 
    ```

请参见下面的单个命令！
[![Single command process monitoring with htop](../Images/9f288dd8ee2d4d70b486425840842a2f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--c1hM-81s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/FepzxVp.gif)

## 双倍奖励:让你的编辑器更漂亮

我在 Twitter 上看到一些关于这篇文章的讨论，其中提到我应该添加关于我在这里使用的终端主题的信息，因为 htop 是由使用的终端和主题决定的。我完全同意，所以这是我的设置:

1.  终端模拟器: [iTerm2](https://www.iterm2.com/) 令人惊叹的 Mac OS 终端替代品，具有网格标签等酷功能。
2.  Unix shell +主题化框架:`zsh` + [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 查看[这篇关于如何安装它们的帖子](https://dev.to/lauragift21/quick-setup-of-oh-my-zsh-terminal-on-ubuntu-2c4e)(在 Ubuntu 或 Mac OS 上类似的步骤)
3.  `oh-my-zsh`主题:默认主题`robbyrussell`。设置好`oh-my-zsh`后，您可以在`~/.zshrc`文件中指定想要的主题，例如`ZSH_THEME="robbyrussell"`