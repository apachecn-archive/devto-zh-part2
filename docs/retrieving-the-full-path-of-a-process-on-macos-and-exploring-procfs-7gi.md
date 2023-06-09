# 在 MacOS 上检索进程的完整路径(并探索 procfs)

> 原文：<https://dev.to/cirowrc/retrieving-the-full-path-of-a-process-on-macos-and-exploring-procfs-7gi>

嘿，

另一天，我试图确保我正在运行的一个给定进程使用的是我构建的特定二进制文件，但是我不知道:`ps`只会向我显示非绝对路径。

```
# How could I know what is the absolute path of the
# `hugo` binary, assuming that I could have multiple
# `hugo` binaries in `$PATH`?
ps
  PID TTY TIME CMD
 4153 ttys000 5:14.98 hugo serve <<<
 9035 ttys001 0:00.04 /Applications/iTerm.app/Content...
 9037 ttys001 0:00.10 -bash
 9086 ttys001 0:02.27 /usr/local/Cellar/macvim/8.1-15...
 9236 ttys002 0:00.04 /Applications/iTerm.app/Content...
 9238 ttys002 0:00.10 -bash 
```

Enter fullscreen mode Exit fullscreen mode

我想，如果我用的是 Linux，那会很简单:转到`/proc`，搜索进程的`pid`，然后检查`exe`链接到什么；完成了。

```
# (on a Linux machine ...)
#
# See `hugo` will still not show up with the absolute path
# like on MacOS.
ps aux | grep hugo
ubuntu 2275 0.0 0.0 101852 748 pts/0 Sl+ 00:26 0:00 hugo serve

# Given that the proc filesystem can provide us with some
# more information about the process, check out the `exe`
# link (which should provide a link to the actual executable).
stat /proc/2275/exe
  File: /proc/2275/exe -> /usr/local/bin/hugo
  Size: 0 Blocks: 0 IO Block: 1024 symbolic link Device: 4h/4d   Inode: 140106 Links: 1
Access: (0777/lrwxrwxrwx) Uid: ( 1001/ ubuntu) Gid: ( 1001/ ubuntu)
Access: 2018-09-24 00:26:37.167004005 +0000
Modify: 2018-09-24 00:26:27.391004005 +0000
Change: 2018-09-24 00:26:27.391004005 +0000
 Birth: - 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章中，我将介绍我们如何在 MacOS 上收集这些信息，以及 Linux 中的`procfs`是怎么回事。

*TL；博士:`/proc`在 Linux 上是 dope 在 MacoS 上:从`libproc`编译一点使用 [`proc_pidpath`](https://opensource.apple.com/source/xnu/xnu-2422.1.72/libsyscall/wrappers/libproc/libproc.h.auto.html) 的代码，或者安装 [`pidpath`](https://github.com/cirocosta/pidpath) 。*

### Linux 中的/proc 文件系统

在“Linux 之地”里，有一个叫“procfs”的东西。

它是一个虚拟文件系统——从某种意义上说，磁盘中没有真正的常规文件映射到文件系统表示——允许用户(在用户空间中)对其当前运行的进程和其他进程进行一些自省。

从[开始，内核文件](https://www.kernel.org/doc/Documentation/filesystems/proc.txt):

> proc 文件系统充当内核内部数据结构的接口。
> 
> 它可以用来获取关于系统的信息，并在运行时更改某些内核参数( [sysctl](https://linux.die.net/man/8/sysctl) )。

与它互动的方式非常巧妙:

*   每个进程在`/proc`(比如，`/proc/<pid>`)下接收一个给定的路径，然后
*   作为该路径的子目录，存在各种其他文件和子目录，以允许对特定 pid 进行更深入的自省。

```
# Display the files and directories present at the
# very root of `/proc`.
#
# Here we can find the list of PIDs that we can access,
# as well as some more system-wide information and 
# settings that we can tweak.
ls -lah /proc
total 4.0K
dr-xr-xr-x 124 root root 0 Sep 24 23:56 .
drwxr-xr-x 24 root root 4.0K Sep 24 23:57 ..
dr-xr-xr-x 9 root root 0 Sep 24 23:56 1
dr-xr-xr-x 9 root root 0 Sep 25 00:54 2016
dr-xr-xr-x 9 root root 0 Sep 24 23:57 417
...
-r--r--r-- 1 root root 0 Sep 25 01:25 sched_debug
-r--r--r-- 1 root root 0 Sep 25 01:25 schedstat
dr-xr-xr-x 4 root root 0 Sep 25 01:25 scsi
lrwxrwxrwx 1 root root 0 Sep 24 23:56 self -> 2574
...

# Getting into a specific pid path, we're able to
# gather more information about the specifics of
# a given process.
ls -lah /proc/472
total 0
dr-xr-xr-x 9 root root 0 Sep 24 23:57 .
dr-xr-xr-x 123 root root 0 Sep 24 23:56 ..
...
lrwxrwxrwx 1 root root 0 Sep 25 01:30 cwd -> /
-r-------- 1 root root 0 Sep 25 01:30 environ
lrwxrwxrwx 1 root root 0 Sep 24 23:57 exe -> /lib/systemd/systemd-udevd
dr-x------ 2 root root 0 Sep 24 23:57 fd
lrwxrwxrwx 1 root root 0 Sep 25 01:30 root -> /
-rw-r--r-- 1 root root 0 Sep 25 01:30 sched
... 
```

Enter fullscreen mode Exit fullscreen mode

不仅如此，当您不确定某个进程是否在您没有预料到的情况下被阻塞时,`procfs`非常有用(比如一个`write(2)`到一个`nfs`挂载点，由于一组坏的服务器没有响应而导致错误),或者像您的进程在您不希望的时候休眠一样简单:

```
# Sleep for 33 days on the background
sleep 33d &
[1] 2786

# Check what's the state of the process
cat /proc/2786/stat
2786 (sleep) S ...
 | | |
 | | `-> state (interruptible sleep)
 | `-> command being run (sleep command)
 `-> process id (the pid we used before)

# Check what's the stack trace (from the kernel
# perspective) that led the process to this 
# sleep state
cat /proc/2786/stack
[<0>] hrtimer_nanosleep+0xd8/0x1d0
[<0>] SyS_nanosleep+0x72/0xa0
[<0>] do_syscall_64+0x73/0x130
[<0>] entry_SYSCALL_64_after_hwframe+0x3d/0xa2
[<0>] 0xffffffffffffffff 
```

Enter fullscreen mode Exit fullscreen mode

### 引擎盖下的过程

虚拟化的有趣之处在于,`procfs`的实现能够动态地生成文件系统的表示——每当您发出类似于`read(2)`的 I/O 调用时，Linux 都会根据您的请求做出响应，无论是给定进程打开的文件描述符列表，还是在进程启动时设置的环境变量列表。

例如，如果跟踪`cat /proc/<pid>/meminfo`的执行，我们可以找到`read(2)`系统调用的路径:

```
# stack trace of `cat /proc/<pid>/meminfo`
        meminfo_proc_show
        proc_reg_read
        __vfs_read
        vfs_read
        sys_read
        do_syscall_64
        entry_SYSCALL_64_after_hwframe

# stack trace of `cat /file.txt` 
# on an ext4 mount point
        ext4_file_read_iter
        __vfs_read
        vfs_read
        sys_read
        do_syscall_64
        entry_SYSCALL_64_after_hwframe 
```

Enter fullscreen mode Exit fullscreen mode

与常规的`read`非常不同(如第二个堆栈跟踪所示)，磁盘上没有真正被访问的文件——只有`meminfo_proc_show`返回与用户请求相关的内容:虚拟内存内容。

顺便说一下，如果你有兴趣了解更多相关的主题，这类知识的一个很好的参考资料是[Linux 编程接口:Linux 和 UNIX 系统编程手册](https://amzn.to/2QWyXp9)。

现在来看看 MacOS。

### MAC OS 中的 libproc 库

与 Linux 不同的是，我们似乎不太了解 MacOS 上的东西是如何工作的。

在搜索了一下如何完成收集流程信息之后，`libproc`出现了。

[`libproc.h`](https://opensource.apple.com/source/xnu/xnu-2422.1.72/libsyscall/wrappers/libproc/libproc.h.auto.html) 中提到:

```
/*
 * This header file contains private interfaces 
 * to obtain process information.
 *
 * These interfaces are subject to change in future releases.
 */ 
```

Enter fullscreen mode Exit fullscreen mode

有一点需要注意:

*   接口是**私有的**——不保证与未来版本的兼容性。

苹果公司的一名工作人员在苹果开发者论坛的[帖子](https://forums.developer.apple.com/thread/46963)中就收集流程信息进行了阐述:

> [……]苹果公司并没有在提供获取这类信息的 API 方面投入太多精力。
> 
> 现有的 API 要么是从 OS X 的前身 OSs 继承而来，要么是为了满足我们的内部需求而添加的**，而不是第三方开发者的需求**。
> 
> 因此，你会发现很多这些 API 的地方:不完整；不正确；文档不完善，二进制不兼容。

无论如何，我们仍然可以利用它——更具体地说，我们可以利用`proc_pidpath`,这是一种采用`pid`(我们想了解更多的进程的`pid`)、应该写入路径的缓冲区以及缓冲区大小的方法。

```
int proc_pidpath(
  int pid, // pid of the process to know more about
  void * buffer, // buffer to fill with the abs path
  uint32_t buffersize // size of the buffer
); 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，我们可以通过指定两个不同的编译目标来创建可以处理 Linux 和 MacOS 的 go 二进制文件。

### 适合 Linux 和 MacOS 的 Golang 二进制

鉴于`libproc`在 Linux 下不会成为一个东西，我们可以从创建一个只在 Linux 上编译的`pidpath_linux.go`文件开始，另一个文件`pidpath_darwin.go`，针对 MacOS 机器。

Linux 非常简单:它跟在`/proc/<pid>/exe`符号链接之后，就是这个:

```
// +build linux
package main

import (
    "os"
    "strconv"
)

func GetExePathFromPid(pid int) (path string, err error) {
    path, err = os.Readlink("/proc/" + strconv.Itoa(pid) + "/exe")
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，MacOS 版本还需要更多。

假设我们通过 C 访问`libproc`，我们可以利用 [CGO](https://golang.org/cmd/cgo/) 。

```
// +build darwin
package main

// #include <libproc.h>
// #include <stdlib.h>
// #include <errno.h>
import "C"

import (
    "fmt"
    "unsafe"
)

// bufSize references the constant that the implementation
// of proc_pidpath uses under the hood to make sure that
// no overflows happen.
//
// See https://opensource.apple.com/source/xnu/xnu-2782.40.9/libsyscall/wrappers/libproc/libproc.c
const bufSize = C.PROC_PIDPATHINFO_MAXSIZE

func GetExePathFromPid(pid int) (path string, err error) {
        // Allocate in the C heap a string (char* terminated
        // with `/0`) of size `bufSize` and then make sure
        // that we free that memory that gets allocated
        // in C (see the `defer` below).
    buf := C.CString(string(make([]byte, bufSize)))
    defer C.free(unsafe.Pointer(buf))

        // Call the C function `proc_pidpath` from the included
        // header file (libproc.h).
    ret, err := C.proc_pidpath(C.int(pid), unsafe.Pointer(buf), bufSize)
    if ret <= 0 {
        err = fmt.Errorf("failed to retrieve pid path: %v", err)
        return
    }

        // Convert the C string back to a Go string.
    path = C.GoString(buf)
    return
} 
```

Enter fullscreen mode Exit fullscreen mode

完成后，我们现在可以在应用程序中使用`GetExePathFromPid`。

要了解这一点，请查看 [cirocosta/pidpath](https://github.com/cirocosta/pidpath) 。

### 关闭思绪

对我来说，了解 MacOS 领域的不同之处是很有趣的。

虽然我将 Macbook Pro 用作个人电脑(以及工作中的 Mac 电脑)，但我并没有真正关注这些小细节。

还有，`/proc`只是**所以**有价值！绝对值得了解更多关于其他功能的信息。请务必阅读[Linux 编程接口:Linux 和 UNIX 系统编程手册](https://amzn.to/2QWyXp9)。

如果你有任何问题，或者改进这篇博文的建议，请告诉我！我是 [cirowrc](https://twitter.com/cirowrc) ，我喜欢聊天。

祝你愉快！