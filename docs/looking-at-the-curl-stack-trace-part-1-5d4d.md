# 查看 curl 堆栈跟踪:第 1 部分

> 原文：<https://dev.to/captainsafia/looking-at-the-curl-stack-trace-part-1-5d4d>

正如我在[上一篇博文](https://dev.to/captainsafia/looking-at-how-curl-works-through-stack-traces-5g49-temp-slug-3937288)中提到的，我希望在接下来的两篇博文中对 curl 的`strace`做一个彻底的分析。我在上一篇博文中开始查看堆栈跟踪，但我很快意识到，查看所有内容需要不止一篇博文，所以我来了。

好了，这是我要查看的堆栈跟踪的第一部分。

```
[pid 8819] set_robust_list(0x7f21c40f09e0, 24) = 0
[pid 8819] socket(AF_UNIX, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
[pid 8819] connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
[pid 8819] close(3) = 0
[pid 8819] socket(AF_UNIX, SOCK_STREAM|SOCK_CLOEXEC|SOCK_NONBLOCK, 0) = 3
[pid 8819] connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
[pid 8819] close(3) 
```

这个堆栈跟踪中的第一个系统调用是`set_robust_list`。我以前从未遇到过这种情况，所以我去找了[医生](https://linux.die.net/man/2/set_robust_list)。

> set_robust_list()系统调用请求内核记录调用线程拥有的健壮 futexes 列表的头部。

啊哈！Futexes。我不久前在系统课上学到了这些。Futexes 是一个低级的多线程构造。它们允许您实现锁定(一种线程要求对资源进行控制的方式，以便其他线程不能同时访问它)。你可以在这里阅读更多关于 futexes】的内容。我猜`curl`进程会产生一些线程来做后台工作，这个`set_robust_list`函数调用与那些即将创建的线程有关。

接下来，程序试图打开两个套接字连接。我不确定这些插座连接有什么意义。在程序中打开任何套接字似乎为时过早。我决定在“/var/run/nscd/socket”上做一些谷歌搜索，看看这是否能阐明套接字连接的用途。我偶然看到[这篇博文](https://jameshfisher.com/2018/02/05/dont-use-nscd.html)，它解释说`nscd`是用来运行本地 DNS 解析器的。本地 DNS 解析程序是一种程序，它在本地计算机上执行特定域的 IP 地址查找。事实证明，打开机器上本地 DNS 服务器的套接字的尝试失败了，因为在我执行`curl`命令的机器上没有运行的服务器。事实证明，这与稍后进行的一些系统调用有关。

说到这里，堆栈跟踪的下一个块看起来像这样。

```
[pid 8819] open("/etc/nsswitch.conf", O_RDONLY|O_CLOEXEC) = 3
[pid 8819] fstat(3, {st_mode=S_IFREG|0644, st_size=497, ...}) = 0
[pid 8819] read(3, "# /etc/nsswitch.conf\n#\n# Example"..., 4096) = 497
[pid 8819] read(3, "", 4096) = 0
[pid 8819] close(3) = 0 
```

我不熟悉`nsswitch.conf`文件。这并不奇怪，这个堆栈跟踪中的很多东西对我来说都是新的。[文档](http://man7.org/linux/man-pages/man5/nsswitch.conf.5.html)显示该配置文件用于存储不同域名解析的详细信息。下面是这个文件中关于`curl`的重要一行。

```
hosts: files dns 
```

这一行说明主机名应该首先通过查看任何配置文件来解析，然后通过 DNS 服务来解析。

堆栈跟踪的下一部分与`nsswitch.conf`文件揭示的内容密切相关。

```
[pid 8819] open("/etc/host.conf", O_RDONLY|O_CLOEXEC) = 3
[pid 8819] fstat(3, {st_mode=S_IFREG|0644, st_size=9, ...}) = 0
[pid 8819] read(3, "multi on\n", 4096) = 9
[pid 8819] read(3, "", 4096) = 0
[pid 8819] close(3) 
```

在这种情况下，`curl`通过`host.conf`进行查找，T1 是一个文件，它规定了如何将主机名解析为 IP 地址。该文件可以有一些垂直配置，但在本例中，它只包含一个`multi on`，这表明存储在 hosts 文件(`/etc/hosts`)中的主机可以引用多个 IP 地址。

堆栈跟踪中的下几行与`nsswitch.conf`中配置的另一部分相关。`curl`命令打开了`resolv.conf`文件，其中包含了一个[域名服务器](https://en.wikipedia.org/wiki/Name_server)的列表，我们的计算机可以通过查询来解析域名。

```
[pid 8819] futex(0x7f21c8974a64, FUTEX_WAKE_PRIVATE, 2147483647) = 0
[pid 8819] getpid() = 8818
[pid 8819] open("/etc/resolv.conf", O_RDONLY|O_CLOEXEC) = 3
[pid 8819] fstat(3, {st_mode=S_IFREG|0644, st_size=111, ...}) = 0
[pid 8819] read(3, "domain c.sky-eniac-f13b2.internal"..., 4096) = 111
[pid 8819] read(3, "", 4096) = 0
[pid 8819] close(3) 
```

总之，`curl`命令执行的前几个系统调用表明，它处理的第一个任务是解析命令中输入的域名的 IP 地址。在这种情况下，是`https://safia.rocks`。我觉得这个点是结束这篇博文的好地方。我将在下一篇博文中继续查看堆栈跟踪的其余部分。见(写？)你接着！