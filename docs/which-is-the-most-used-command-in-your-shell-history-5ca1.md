# 您的 shell 历史中使用最多的命令是什么？

> 原文：<https://dev.to/abhinav/which-is-the-most-used-command-in-your-shell-history-5ca1>

最近我在 reddit 上看到一个[主题](https://www.reddit.com/r/linux/comments/8vzr3y/whats_the_most_frequent_terminal_command_you_use/)，问用户在 shell 历史中使用最多的命令。

我的结果:

```
 1  419  13.0489%    git
     2  265  8.25288%    cd 3  239  7.44316%    sudo 4  122  3.79944%    cat 5  77   2.39801%    which
     6  76   2.36686%    dkr
     7  72   2.24229%    rm 8  60   1.86858%    rg
     9  57   1.77515%    yarn
    10  57   1.77515%    nvim 
```

Enter fullscreen mode Exit fullscreen mode

显然`git`是我最常用的命令，因为我是在工作笔记本电脑上运行它的。应该更多，但我最近一直在使用 prezto 的`git`别名。其他常见的命令有`dkr`，它是`sudo docker`和`rg`的别名，是 [ripgrep](https://dev.to/abhinav/rest-in-peace-grep-19h9) 的二进制代码。

我想知道 dev.to 社区怎么样？

使用来自[linux.byexample.com](http://linux.byexamples.com/archives/332/what-is-your-10-common-linux-commands/)的这个命令来列出你最常用的命令:

```
history | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl |  head -n10 
```

Enter fullscreen mode Exit fullscreen mode

我使用 zsh，但它对我不起作用，因为 zsh 的`history`命令只打印一些最近的命令。我不得不稍微调整一下:

```
history 1 | cat | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a; }' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl | head -n10
# or
fc -l 1 | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a; }' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl | head -n10 
```

Enter fullscreen mode Exit fullscreen mode