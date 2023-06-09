# 在 Linux 上找到管道的另一端

> 原文：<https://dev.to/hoelzro/finding-the-other-end-of-a-pipe-on-linux-147a>

*最初发布于[https://hoelz . ro/blog/finding-the-other-end-of-a-pipe-on-Linux](https://hoelz.ro/blog/finding-the-other-end-of-a-pipe-on-linux)T3】*

您是否曾经使用过命令行并看到一些彩色输出，但当您将输出通过管道传输到另一个命令时却失去了颜色？我经常对自己说“如果一个程序能够检测到管道的另一端是否能够处理彩色输出，那会怎么样？”。

据我所知，一个程序没有办法发布这些信息，即使有，我也非常怀疑有多少程序使用它。所以，让我们看看我们是否能完成下一个最好的事情:检测谁在标准输出的另一边听；如果我们知道这一点，我们就可以对照已知支持颜色的程序的白名单来检查该程序的名称和命令行。

因此...我们如何检测谁在听？

# 天真的方法

特定于 Linux 的基本方法(但移植到其他操作系统可能不会太难)非常简单:只需爬过`/proc`并寻找与我们的标准输出管道相匹配的管道。你看，`/proc`下的管道文件描述符和常规文件一样，仍然有 inode，并且 inode 对于管道对是唯一的(套接字也是这样；在`/proc`中还有其他文件引用了这些信息，比如`/proc/net/tcp`。这里有一个快速而愚蠢的 Perl 程序找到了它的合作伙伴:

```
#!/usr/bin/env perl

use strict;
use warnings;
use feature qw(say);

use File::stat;

exit unless -p STDOUT; # bail out if standard output isn't a pipe

my $stdout_stat = stat(\*STDOUT);

my $current_pid = $$;

my @other_procs = grep {
    m{/proc/(\d+)} && $1 != $current_pid
} glob('/proc/*/');

for my $proc_dir (@other_procs) {
    my $stat = stat("$proc_dir/fd/0");
    next unless $stat;

    if($stat->dev == $stdout_stat->dev && $stat->ino == $stdout_stat->ino) {
        my ( $pid ) = $proc_dir =~ m{/proc/(\d+)};

        say STDERR $pid;
        last;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就完成了工作，但对我的口味来说，感觉有点太不舒服了。
一定有更好的办法！

# 不太天真的方法

作为一种更干净方法的灵感，让我们考虑一个管道的表亲:UNIX
套接字。套接字的工作很像管道，因为两个文件描述符被绑定在一起
。有了 UNIX 套接字，我们可以调用`getsockopt(socket, SOL_SOCKET, SO_PEERCRED, &creds, sizeof(struct ucred))`来找出我们在和谁通话；或许对管道也有类似的称呼？

为了弄清楚这个功能是否适用于管道，让我们直接从源代码——确切地说，是 Linux 内核源代码——得到答案！(我正在查看 Linux 4.5.3 的源代码)

如果我们在内核的`.c`文件中寻找`SO_PEERCRED`，我们很快就会在`sock_getsockopt`函数中遇到这个 case 语句:

```
case SO_PEERCRED:
{
        struct ucred peercred;
        if (len > sizeof(peercred))
                len = sizeof(peercred);
        cred_to_ucred(sk->sk_peer_pid, sk->sk_peer_cred, &peercred);
        if (copy_to_user(optval, &peercred, len))
                return -EFAULT;
        goto lenout;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们跟踪`sk`变量的面包屑，我们可以在前面的`sock_getsockopt`函数中看到`sk`被定义为一个`struct sock`，它非常大，但是包含了`struct pid sk_peer_pid`成员，我们希望在任何表示管道的结构中找到它的对应部分。

如果我们寻找特定于管道的`ioctl`或`fcntl`代码，我们可以很容易地找到哪个结构。如果您在`pipe(7)`的手册页中查找，您会找到`F_GETPIPE_SZ`。在内核代码中搜索，我们会看到`fs/pipe.c`中的`pipe_fcntl`，它引用了`struct pipe_inode_info`，这个结构足够小，可以在这里显示:

```
struct pipe_inode_info {
    struct mutex mutex;
    wait_queue_head_t wait;
    unsigned int nrbufs, curbuf, buffers;
    unsigned int readers;
    unsigned int writers;
    unsigned int files;
    unsigned int waiting_writers;
    unsigned int r_counter;
    unsigned int w_counter;
    struct page *tmp_page;
    struct fasync_struct *fasync_readers;
    struct fasync_struct *fasync_writers;
    struct pipe_buffer *bufs;
    struct user_struct *user;
}; 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，粗略地看一下这个结构，我们会发现`struct pipe_inode_info`没有我们需要的信息，所以我们必须坚持我们的天真方法。所以我们没有得到一个更干净的方法，但至少我们得到了一些内核代码！