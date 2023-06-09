# 引擎盖下的 Dmesg

> 原文：<https://dev.to/cirowrc/dmesg-under-the-hood-n9n>

嘿，

本周，我想更多地了解`dmesg`在幕后是如何工作的。在过去，我希望有基于在`dmesg`弹出的错误消息的警报，所以，也许通过尝试创建一些使用与`dmesg`在引擎盖下使用的相同的东西，我可以更好地理解它。

另外，我知道在一些系统中，可以从`kern.log`收集相同的信息，并且`dmesg`是关于读取“内核环形缓冲区”，但是，这是什么意思呢？

更具体地说，`dmesg`是从哪里读到的？

在这篇博客文章中，我通过一些探索来更好地理解整体情况:

*   从内核空间生成日志
*   从 iptables 生成日志
*   从 Linux 可加载内核模块生成日志
*   从 kmsg 生成日志
*   从用户空间查看内核日志
*   解释 kmsg 消息

> 如果你有任何问题，在 Twitter 上给我留言 [@cirowrc](https://twitter.com/cirowrc) ！

### 从内核空间产生日志

每当 Linux 内核中的某个东西(可能是一个[模块](https://en.wikipedia.org/wiki/Loadable_kernel_module))想要做相当于 [`printf`](https://linux.die.net/man/3/printf) 的事情时，可以使用一个类似的实用程序(`printk`)。

这允许程序将信息反馈给用户空间中的开发人员(或任何其他用户),以了解模块发生了什么——可以是警告消息、堆栈跟踪或一些纯粹的调试信息。

为了测试从内核生成日志，我想到了一些想法:

1.  [`iptables`](https://linux.die.net/man/8/iptables) 有一个 [`LOG`目标](http://ipset.netfilter.org/iptables-extensions.man.html#lbDD)，当目标被瞄准时，它会从`iptables`自身生成一些日志。作为运行在内核空间的`iptables`(不是`cli`，我的意思是——实际的东西)，我们可以看到日志从内核中出来；

2.  编写一个最小的内核模块应该不是很难的事情——以前很多人都这样做过。我可以写一个`printk("hello-world")`就这样！

3.  检查一下是否有更简单的方法可以用来生成日志(也许内核公开了一个标准接口来做这件事？)

既然这是一次探索，为什么不尝试所有的方法呢？

### 从 iptables 生成日志

虽然这篇博客文章并不是特别关于 iptables，但是我们可以把它作为一个例子，对于坐在用户空间的用户来说，与内核深处正在进行的事情进行交互是很有趣的。

来自 [`iptables`手册页](https://linux.die.net/man/8/iptables):

> Iptables 用于设置、维护和检查 Linux 内核中的 IP 包过滤规则表。可以定义几个不同的表。

这些提到的规则可以是非常简单的事情(丢弃来自给定来源的数据包)，也可以是依赖于一系列条件的非常复杂的规则链。

认识到能够可视化违反特定规则链的数据包非常有用，`LOG`目标应运而生。

> 日志:打开匹配数据包的内核日志。
> 
> 当为规则设置该选项时，Linux 内核将通过内核日志打印所有匹配数据包的一些信息(像大多数 IP/IPv6 报头字段一样)。

这里有一个例子可以让我们看到它是如何工作的:

```
# List the current state of the `filter` table.
# As we can see below, nothing is set up:
# - any packet arriving w/ our machine as the
# destination is accepted;
# - any packet arriving that should be forwarded
# is dropped;
# - any packet that is sent from our machine is
# accepted (can go through).
iptables --table filter --list
Chain INPUT (policy ACCEPT)
target prot opt source destination

Chain FORWARD (policy DROP)
target prot opt source destination

Chain OUTPUT (policy ACCEPT)
target prot opt source destination

# Add a rule to the OUTPUT chain to log every packet
# that is destined towards 8.8.8.8 (google's public
# dns) 
iptables \
        --table filter \
        --insert OUTPUT \
        --jump LOG \
        --destination 8.8.8.8 \
        --log-prefix="[google-dns-out]"

# Check that the rule has been placed in the OUTPUT 
# chain of the `filter` table
iptables --table filter --list OUTPUT --numeric
Chain OUTPUT (policy ACCEPT)
target prot opt source destination
LOG all -- 0.0.0.0/0 8.8.8.8 LOG flags 0 level 4 prefix "[google-dns-out]"

# Perform a DNS query targetting 8.8.8.8
dig example.com @8.8.8.8
... example.com. 18272  IN  A   93.184.216.34

# Verify the request packet in our logs (dmesg)
dmesg | grep google-dns-out
[66458.608136] [google-dns-out]IN= OUT=enp0s3 SRC=10.0.2.15 DST=8.8.8.8 LEN=80 TOS=0x00 PREC=0x00 TTL=64 ID=30602 PROTO=UDP SPT=39573 DPT=53 LEN=60 
```

Enter fullscreen mode Exit fullscreen mode

### 从 Linux 可加载内核模块生成日志

我自己不是驱动程序开发人员(老实说，以前从未编写过内核模块)，寻找可以教我如何完成这项任务的资源是一件有趣的事情。

根据 Linux 内核实验室的指南，我为一个非常简单的内核模块编写了下面的代码:

```
/**
 * `module.h` contains the most basic functionality needed for
 * us to create a loadable kernel module, including the `MODULE_*`
 * macros, `module_*` functions and including a bunch of other
 * relevant headers that provide useful functionality for us
 * (for instance, `printk`, which comes from `linux/printk.h`,
 * a header included by `linux/module.h`).
 */

#include <linux/module.h> 
/**
 * Following, we make use of several macros to properly provide
 * information about the kernel module that we're creating.
 *
 * The information supplied here are visible through tools like
 * `modinfo`.
 *
 * Note.: the license you choose here **DOES AFFECT** other things -
 * by using a proprietary license your kernel will be "tainted".
 */
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Ciro S. Costa");
MODULE_DESCRIPTION("A hello-world printer");
MODULE_VERSION("0.1");

/** hello_init - initializes the module
 *
 * The `hello_init` method defines the procedures that performs the set up
 * of our module.
 */
static int
hello_init(void)
{
         // By making use of `printk` here (in the initialization),
         // we can look at `dmesg` and verify that what we log here
         // appears there at the moment that we load the module with
         // `insmod`.
    printk(KERN_INFO "HELLO-WORLD: Hello world!\n");
    return 0;
}

static void
hello_exit(void)
{
        // similar to `init`, but for the removal time.
    printk(KERN_INFO "HELLO-WORLD: Bye bye world!\n");
}

// registers the `hello_init` method as the method to run at module
// insertion time.
module_init(hello_init);

// similar, but for `removal`
module_exit(hello_exit); 
```

Enter fullscreen mode Exit fullscreen mode

*ps。:源代码可在[github.com/cirocosta/hello-world-lkm](https://github.com/cirocosta/hello-world-lkm)T3】获得*

加载模块，就可以了:

```
dmesg | grep HELLO-WORLD
[62076.224353] HELLO-WORLD: Hello world! 
```

Enter fullscreen mode Exit fullscreen mode

### 从 kmsg 生成日志

我们可以用来将消息插入到`printk`缓冲区的标准接口与我们可以用来从中读取消息的接口完全相同:`/dev/kmsg`。

来自[内核文档](https://www.kernel.org/doc/Documentation/ABI/testing/dev-kmsg):

> 注入消息:对打开的设备节点的每个 write()都会在内核的 printk 缓冲区中放置一个日志条目。

也就是说，无论我们`echo "haha"`作为单个`write`到`/dev/kmsg`进入缓冲区。

那我们试试:

```
# Generate the message from my current unprivileged
# user and then pipe to the privileged `tee` which is
# able to write into `/dev/kmsg` (yep, /dev/kmsg can be
# see and read, but to write you need more privileges)
echo "haha" | sudo tee "/dev/kmsg"

# Check that we can see our message is indeed there:
dmesg | grep haha
[67030.010334] haha 
```

Enter fullscreen mode Exit fullscreen mode

### 从用户空间查看内核日志

我之前在博客中提到了`kmsg`，这已经破坏了这篇博文，现在没有什么可说的了。

内核在用户空间中为我们提供了`/dev/kmsg`,这是一个特殊的设备，允许我们从环形缓冲区中读取数据(考虑到多租户)，这就是 dmesg 所使用的。

```
# Trace the syscalls `openat` and `read` to verify
# that it reads from `/dev/kmsg`
strace -e openat,read -f dmesg > /dev/null
openat(AT_FDCWD, "/dev/kmsg", O_RDONLY|O_NONBLOCK) = 3
read(3, "5,0,0,-;Linux version 4.15.0-34-"..., 8191) = 192
read(3, "6,1,0,-;Command line: BOOT_IMAGE"..., 8191) = 142
read(3, "6,2,0,-;KERNEL supported cpus:\n", 8191) = 31
read(3, "6,3,0,-; Intel GenuineIntel\n", 8191) = 29 
```

Enter fullscreen mode Exit fullscreen mode

更深入地说，每当针对这样的文件发出一个`read(2)`系统调用时，内核就在内部触发 [`kernel/printk/devkmsg_read`](https://elixir.bootlin.com/linux/v4.18.8/source/kernel/printk/printk.c#L823) ，从循环队列中取出一条消息，将其格式化，然后发送回用户。

```
# We can use `iovisor/bcc#examples/trace/stacknoop.py` to
# gather the stack trace from the execution of `devkmsg_read`
# to verify that the `read` gets right there when `vfs_read`
# is called.
./stacksnoop.py -v devkmsg_read
TIME(s) COMM PID CPU FUNCTION
1.875072956 tail 1565 0 devkmsg_read
    devkmsg_read
    vfs_read
    sys_read
    do_syscall_64
    entry_SYSCALL_64_after_hwframe 
```

Enter fullscreen mode Exit fullscreen mode

当写入循环队列时也遵循类似的路径:每当发出`write(2)`时，在某个点 [`devkmsg_write`](https://elixir.bootlin.com/linux/v4.18.8/source/kernel/printk/printk.c#L760) 被调用。

`devkmsg`将这样的调用转换为相当于`printk`的调用，然后通过到达`log_store`的路径，该方法最终从队列中获取一个空闲空间并添加日志消息:

```
# In one terminal
echo "haha" > /dev/kmsg

# In another terminal
./stacksnoop.py -v log_store
TIME(s) COMM PID CPU FUNCTION
1.786375046 bash 1450 2 log_store
    log_store
    printk_emit
    devkmsg_write
    new_sync_write
    __vfs_write
    vfs_write
    sys_write
    do_syscall_64
    entry_SYSCALL_64_after_hwframe 
```

Enter fullscreen mode Exit fullscreen mode

知道了内核提供了这个接口，我们就可以实现一个简单的程序，不断地对这样的设备执行`read(2)` s，然后解析到达的内容(消息)。

### 解释 kmsg 消息

知道对`/dev/kmsg`执行的每个`read(2)`系统调用都返回给我们一条消息，我们剩下的工作是:-实现一个读取器，它持续查看`/dev/kmsg`，然后从那里提取原始消息；以及-实现这些消息的解析。

在我看来，后者是最有趣的。

每条消息都以下列格式打包:逗号分隔的信息字段列表和一条消息(这些字段用分号分隔)。

```
//
//                  INFO                      MSG
//     .------------------------------------------. .------.
//    |                                            |        |
//    | int int      int      char, <ignore>   | string |
//    prefix,   seq, timestamp_us,flag[,..........];<message>
// 
```

Enter fullscreen mode Exit fullscreen mode

info 部分有四个标准字段，为将来的其他字段留出了空间。

从`priority`字段中，我们可以提取两条信息:优先级和设施。

```
// DecodePrefix extracts both priority and facility from a given
// syslog(2) encoded prefix.
//
//     facility    priority
//      .-----------.  .-----.
//      |           |  |     |
//  7  6  5  4  3  2  1  0    bits 
```

Enter fullscreen mode Exit fullscreen mode

利用其余的字段，我们能够知道消息是何时产生的，以及在已经放入缓冲区的消息序列中，这样的消息携带什么 ID。

如果您有兴趣以实际代码的形式查看这种解析，我写了一个 Golang 实现，您可以在这里查看:[github.com/cirocosta/dmesg_exporter/kmsg/kmsg.go](https://github.com/cirocosta/dmesg_exporter/blob/fc7962f46d304c158f21cc71cf2049af7d183c0a/kmsg/kmsg.go#L178)。

### 关闭思绪

有机会深入堆栈并验证来自内核的组件如何与用户空间应用程序进行通信是非常棒的。

这促使我创建了 [`dmesg_exporter`](https://github.com/cirocosta/dmesg_exporter) ，这是一个用于导出“dmesg”日志指标的工具，以便人们最终可以提醒来自特定设施的标记为“关键”的消息。

在写这篇博文的过程中，我使用了以下书籍:[Linux 编程接口](https://amzn.to/2QWyXp9)。请务必检查它！这绝对是关于 Linux 的最好的书籍之一。

如果您有任何问题，或者有改进的想法，请告诉我！我是推特上的 cirowrc ,我很想收到你的来信！

祝你愉快！