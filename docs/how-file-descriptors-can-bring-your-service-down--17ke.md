# 文件描述符如何导致服务中断。

> 原文：<https://dev.to/thomaswdmelville/how-file-descriptors-can-bring-your-service-down--17ke>

在过去的一年里，我完成了 Linux 基金会的 Linux 管理基础课程。今天我将关注文件描述符和你的应用程序如何创建它们，以及**应该**关闭它们。

让我们从 linux 操作系统如何看待它的世界的一个非常高层次的定义开始。

> 运行中的 linux 系统中的一切都是文件。

文件描述符是 linux 内核为进程打开和关闭的每个文件创建和删除的文件。

> linux 内核允许有限数量的文件描述符！

让我们来看看实际情况。启动您的应用程序并获取 PID。

```
java -jar app.jar
ps -ef | grep app.jar 
```

现在，将 cd 放入进程目录并执行 ls。

```
cd /proc/3535
ls -al 
```

这是内核需要的关于你正在运行的应用的所有信息。

让我们关注文件描述符目录

```
ls -al /proc/3535/fd
ls -al /proc/3535/fd | wc -l 
```

您将看到有许多打开的文件描述符。

使用你的应用程序一会儿，然后回到这个目录，再次执行上面的命令。

现在有多少个描述符？

再次回到你的应用程序，并使用它。

现在有多少？

如果这个数字继续上升，你就有麻烦了！

最终你的应用程序将不能做任何事情，因为它不能打开任何文件描述符。

根据我的经验，主要原因是 i/o 流。

那么，我们如何解决这个问题呢？

轻松点。

关闭你的溪流。

# Java

[用资源试一试](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)来拯救。在 try-with-resources 块中打开你的所有流，jvm 将为你处理关闭流的问题

```
//...

    try (InputStream data = new InputStream()){
        // ...
    } 
```

# 巨蟒

Python 也有类似的概念

```
# ... 
    with open('mine.txt', r) as f:
        # ... 
```

我相信其他语言也有同样的概念，所以请随意评论，我会更新帖子。

因此，**永远关闭你的流，这样你的应用程序才不会最终崩溃。**