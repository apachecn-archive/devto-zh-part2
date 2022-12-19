# 安装时的 OpenBSD 手动分区

> 原文：<https://dev.to/nabbisen/openbsd-manual-partitioning-at-installation-4cif>

[![partitioning section when installing openbsd](../Images/a9525c4ab6dc1ecffe78a0b7385b8be2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Sb9YL0lo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/54u4m2uvpu16ucbe04hb.png)

安装 OpenBSD 时，在分区部分，安装程序会询问选择哪个选项:

1.  使用自动布局
2.  (E)编辑自动布局
3.  创建自定义布局

第一个“a”是一个很好的选择，它依赖于默认设置并节省安装时的操作时间。

然而，我有时喜欢选择手动分区。
例如，使用存储受限的虚拟机，我想让特定挂载点(如`/var`和`/home`)的大小尽可能大。
在这样的时候，“e”或者“c”也是不错的选择。

*   “e”:使用自动布局，但要进行配置。
*   “c”:从一开始就创建我自己的布局。

选项后面是 [`disklabel`](https://man.openbsd.org/disklabel) 运行:

[![disklabel](../Images/9fe28a1c42c26728b792926af6180519.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aHwOB3_s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jqzq377do29cpqo2302s.png)

我们可以通过这些命令管理分区:

```
? | h                # show help
2.7G
p                    # print partitions

a [Partition Label]  # add partition
d [Partition Label]  # delete partition
z                    # delete all partitions

x                    # exit & lose changes
q                    # quit & save changes 
```

Enter fullscreen mode Exit fullscreen mode

今天，我为 Google Compute Engine (GCE)创建了 OpenBSD vm 镜像，它的存储大小被限制在 30gb。
我改变了挂载点及其大小，如下所示:

```
> a a                  # add parition labeled as 'a'
offset: [64]           # just click Enter key
size: [62910476] 1.0G  # set partition size
FS type: [4.2BSD]      # just click Enter key
mount point: [none] /  # set mount point

> a b                  # add parition labeled as 'b'
offset: [2104512]      # just click Enter key
size: [60806028] 1.1G
FS type: [swap]        # just click Enter key

# skip 'c'

> a d                  # add parition labeled as 'd'

...

> p                    # check partitions in the end
> q                    # save changes and exit 
```

Enter fullscreen mode Exit fullscreen mode

最后，我把整个磁盘改成这样:

| 分区标签 | 挂载点 | 以前 | 在...之后 |
| --- | --- | --- | --- |
| a | `/` | 1.0 克 | 1.0 克 |
| b | *T2`swap`* | 1.2G | 1.1G |
| c | *T2`(unused)`* | - | - |
| d | `/tmp` | 1.7 克 | 1.6 克 |
| e | `/var` | 2.7 克 | 10.0 克 |
| f | `/usr` | 1.9 克 | 1.6 克 |
| g | `/usr/X11R6` | 0.9 克 | 0.3G |
| h | `/usr/local` | 4.0 克 | 4.0 克 |
| 我 | `/usr/src` | 1.7 克 | 1.0 克 |
| j | `/usr/obj` | 5.8G | 3.0 克 |
| k | `/home` | 9.1G | 6.4G |
|  | (总和) | 30.0 克 | 30.0 克 |

因此，我在 GCE 上构建了 OpenBSD，并发布了一个小型数据库服务器，它的存储容量足够我使用: )

**更新:**

| 分区标签 | 挂载点 | 以前 | 在...之后 |
| --- | --- | --- | --- |
| a | `/` | 1.0 克 | 0.8G |
| b | *T2`swap`* | 1.2G | 0.8G |
| c | *T2`(unused)`* | - | - |
| d | `/tmp` | 1.7 克 | 1.4G |
| e | `/var` | 2.7 克 | 2.4G |
| f | `/var/www` | - | 7.6 克 |
| f -> g | `/usr` | 1.9 克 | 1.6 克 |
| g -> h | `/usr/X11R6` | 0.9 克 | 0.4G |
| h -> i | `/usr/local` | 4.0 克 | 3.5G |
| i -> j | `/usr/src` | 1.7 克 | 1.0 克 |
| j -> k | `/usr/obj` | 5.8G | 3.0 克 |
| k -> l | `/home` | 9.1G | 7.5G |
|  | (总和) | 30.0 克 | 30.0 克 |

当使用 Google Cloud SQL 作为数据库时，这些任务对于小型虚拟机也很有用: )