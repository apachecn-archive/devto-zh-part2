# 查看 curl 如何通过堆栈跟踪工作

> 原文：<https://dev.to/captainsafia/looking-at-how-curl-works-through-stack-traces-1ha9>

已经是周末了！呜哇！我对这个周末非常兴奋。这是我这些天唯一一次在 Zarf 上完成大量编码工作。

堆栈跟踪中的前几行相关内容是不言自明的。它们捕获将命令写入标准输入(通过文件描述符 2 定义)。引起我兴趣的一个电话是`pselect6`电话，主要是因为我以前没有遇到过它。原来`pselect6`负责确定一组文件描述符中的文件描述符是否准备好进行交互。前两个命令是要观察的文件描述符的数量，第二个是要观察的文件描述符的集合。在这种情况下，`pselect`只是观察文件描述符 0 的状态。文件描述符 0 是标准的输入文件描述符，所以我假设这个系统调用会检查用户是否没有主动输入命令。

```
write(2, "curl https://safia.rocks", 24) = 24
pselect6(1, [0], NULL, NULL, NULL, {[], 8}) = 1 (in [0])
read(0, "\r", 1) = 1
write(2, "\n", 1) = 1 
```

堆栈跟踪中的下几行调用一个命令，我很熟悉这个命令，它使用了一组非常有趣的参数。正如在其他博客文章中提到的，`ioctl`是一个命令，它是

```
ioctl(0, TCGETS, {B38400 opost isig -icanon -echo ...}) = 0
ioctl(0, SNDCTL_TMR_STOP or TCSETSW, {B38400 opost isig icanon echo ...}) = 0
ioctl(0, TCGETS, {B38400 opost isig icanon echo ...}) = 0 
```

接下来的几行是这个的变体。我从来没有遇到过`rt_sigaction`

```
rt_sigaction(SIGINT, {sa_handler=0x467410, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7f
3750fe6060}, {sa_handler=0x4bb540, sa_mask=[], sa_flags=SA_RESTORER, sa_restorer=0x7f3750fe60
60}, 8) = 0 
```

堆栈跟踪中接下来的几行让我意识到在运行我的`curl`命令之前，我没有正确配置`strace`。在下面的堆栈跟踪中，有一个对`clone`系统调用的调用，它创建了一个新的进程线程。这个新线程创建后不久，堆栈跟踪就结束了。这让我意识到，许多与网络相关的工作一定是在那个线程中进行的。特别是，我在寻找与打开套接字相关的系统调用和来自它们的消息。

```
pipe([3, 4]) = 0
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0
x7f375199ae10) = 13439 = 0 
```

我返回并使用适当的标志重新运行了`strace`命令，即`f`标志，它告诉`strace`跟踪子进程和线程。当我这样做时，我得到了一个更长的堆栈跟踪，并且有许多我期望看到的系统调用。首先发生的与网络相关的事情之一与配置有关。在这种情况下，它会加载用户为 curl 设置的默认配置。

```
[pid 13601] open("/usr/lib/ssl/openssl.cnf", O_RDONLY) = 3
...
[pid 13601] open("/home/safia/.curlrc", O_RDONLY) 
```

当我浏览剩余的堆栈跟踪时，我意识到这是一种应该在多个系列中分析的事情。考虑到这些天我不得不写的时间，我不得不把我的博客写得很短。我不太介意这个。在这种情况下，它将允许我解析出这个相当大的堆栈跟踪，而不会丢失它。

敬请关注！