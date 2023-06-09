# 从 Linux OOM 中幸存下来黑仔

> 原文：<https://dev.to/rrampage/surviving-the-linux-oom-killer-2ki9>

当你的 Linux 机器内存不足时，内核调用内存不足(OOM)杀手来释放一些内存。在运行大量内存密集型进程的服务器上经常会遇到这种情况。在这篇文章中，我们深入探讨了 OOM killer 何时被调用，它如何决定杀死哪个进程，以及我们是否可以阻止它杀死像数据库这样的重要进程。

### OOM 黑仔如何选择杀死哪个进程？

Linux 内核给每个正在运行的进程一个名为`oom_score`的分数，这个分数显示了在可用内存不足的情况下它被终止的可能性。分数与进程使用的内存量成比例。比分是`10 x percent of memory used by process`。所以最高分是 100% x 10 = 1000。此外，如果一个进程作为一个**特权用户**运行，那么它得到的**oom _ score**比一个普通用户进程使用的相同内存稍低。在早期版本的 Linux(2 . 6 . 32 内核)中，有一个更复杂的启发式算法来计算这个分数。

流程的`oom_score`可以在`/proc`目录中找到。假设您的进程的进程 id (pid)是 42，`cat /proc/42/oom_score`将会给您进程的分数。

### 我能确保一些重要的流程不会被 OOM 黑仔扼杀吗？

是啊！OOM 杀手检查`oom_score_adj`来调整它的最终计算分数。该文件存在于`/proc/$pid/oom_score_adj`中。您可以在这个文件中添加一个较大的负值，以确保您的进程被 OOM killer 选中和终止的几率较低。`oom_score_adj`可以从-1000 到 1000 不等。如果你给它分配-1000，它可以使用 100%的内存，仍然可以避免被 OOM killer 终止。另一方面，如果你给它分配 1000，Linux 内核将继续杀死进程，即使它使用最小的内存。

让我们回到 pid 42 的流程。下面是你如何改变它的`oom_score_adj` :

```
echo -200 | sudo tee - /proc/42/oom_score_adj 
```

Enter fullscreen mode Exit fullscreen mode

我们需要以`root`用户或`sudo`的身份这样做，因为 Linux 不允许普通用户降低 OOM 分数。您可以作为普通用户增加 OOM 分数，无需任何特殊权限。例如`echo 100 > /proc/42/oom_score_adj`

还有另一个不太精细的分数叫做`oom_adj`，从-16 到 15 不等。类似于`oom_score_adj`。事实上，当你设置`oom_score_adj`时，内核会自动将其缩小并计算`oom_adj`。`oom_adj`的魔法值为-17，表示给定的进程永远不会被 OOM killer 杀死。

### 显示所有运行过程的 OOM 分数

该脚本显示所有正在运行的进程的 OOM 分数和 OOM 调整分数，按照 OOM 分数的降序排列

```
 #!/bin/bash
# Displays running processes in descending order of OOM score
printf 'PID\tOOM Score\tOOM Adj\tCommand\n'
while read -r pid comm; do [ -f /proc/$pid/oom_score ] && [ $(cat /proc/$pid/oom_score) != 0 ] && printf '%d\t%d\t\t%d\t%s\n' "$pid" "$(cat /proc/$pid/oom_score)" "$(cat /proc/$pid/oom_score_adj)" "$comm"; done < <(ps -e -o pid= -o comm=) | sort -k 2nr 
```

Enter fullscreen mode Exit fullscreen mode

### 检查你的进程是否被 OOM 杀死了

最简单的方法是`grep`你的系统日志。在 Ubuntu: `grep -i kill /var/log/syslog`。如果一个进程被终止，你可能会得到类似`my_process invoked oom-killer: gfp_mask=0x201da, order=0, oom_score_adj=0`的结果

### OOM 分数调整注意事项

请记住，OOM 是一个更大问题的征兆——可用内存不足。解决这个问题的最好方法是增加可用内存(例如更好的硬件)，或者将一些程序转移到其他机器上，或者减少程序的内存消耗(例如尽可能少分配内存)。

对 OOM 调整分数的过多调整将导致随机进程被杀死，并且不能释放足够的内存。

### 参考文献

1.  [proc](http://man7.org/linux/man-pages/man5/proc.5.html) 手册页
2.  [https://askubuntu . com/questions/60672/how-do-I-use-oom-score-adj/](https://askubuntu.com/questions/60672/how-do-i-use-oom-score-adj/)
3.  [演练](https://linux-mm.org/OOM_Killer)Linux 代码的哪一部分被调用
4.  经典 [LWN 文章](https://lwn.net/Articles/317814/)(有点过时)
5.  [手动调用 OOM 杀手](https://www.lynxbee.com/how-to-invoke-oom-killer-manually-for-understanding-which-process-gets-killed-first/)