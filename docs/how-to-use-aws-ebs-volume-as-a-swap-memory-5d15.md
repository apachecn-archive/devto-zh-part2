# 如何使用 AWS EBS 卷作为交换内存

> 原文：<https://dev.to/hardiksondagar/how-to-use-aws-ebs-volume-as-a-swap-memory-5d15>

### 什么是交换空间？

当系统需要的内存多于实际分配的内存(RAM)时，可以使用 Linux 中的交换空间。启用交换空间后，Linux 系统可以将不常用的内存页面从物理内存交换到交换空间(专用分区或现有文件系统中的交换文件),并为需要高速访问的内存页面释放空间。[ [1](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-store-swap-volumes.html)

### 步骤 1 -创建 EBS 卷

按照步骤在 AWS 控制台中创建 EBS 卷，我们将把它用作交换内存。

1.  转到 EC2 仪表板
2.  选择 EBS 卷
3.  创建卷
4.  交换卷的推荐大小是 RAM 的两倍[[2](https://askubuntu.com/questions/49109/i-have-16gb-ram-do-i-need-32gb-swap)
5.  选择与您的 EC2 实例相同的可用性区域

### 步骤 2 -将 EBS 卷连接到 EC2

选择我们刚刚为 swap 和您的 EC2 实例创建的 EBS 卷。

### 步骤 3 -将 EBS 卷设置为交换空间

运行`lsblk`检查新卷，它将在实例中显示为`/dev/xvdf`。

```
$ lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  256G  0 disk
└─xvda1 202:1    0  256G  0 part /
xvdf    202:80   0   32G  0 disk 
```

Enter fullscreen mode Exit fullscreen mode

设置交换区

```
$ sudo mkswap /dev/xvdf 
```

Enter fullscreen mode Exit fullscreen mode

启用交换

```
$ sudo swapon /dev/xvdf 
```

Enter fullscreen mode Exit fullscreen mode

通过在`/etc/fstab`
中添加以下行，使该交换设置保持不变

```
$ sudo nano /etc/fstab
/dev/xvdf none swap sw 0 0 
```

Enter fullscreen mode Exit fullscreen mode

### 第 4 步-检查交换空间

```
$ sudo swapon --show
NAME      TYPE      SIZE   USED PRIO
/dev/xvdf partition  32G 279.9M   -1 
```

Enter fullscreen mode Exit fullscreen mode