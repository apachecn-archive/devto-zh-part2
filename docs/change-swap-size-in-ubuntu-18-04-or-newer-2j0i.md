# 在 Ubuntu 18.04 或更新版本中更改交换空间大小

> 原文：<https://dev.to/bogdancornianu/change-swap-size-in-ubuntu-18-04-or-newer-2j0i>

*【2020 年 7 月 26 日更新】:更改 swapfile 权限；在/etc/fstab 中设置交换文件。*

交换区是计算机上的一个特殊区域，操作系统可以将它用作额外的 RAM。

从 Ubuntu 17.04 开始，交换分区被交换文件取代。交换文件的主要优点是容易调整大小。

**注意:**在运行以下命令之前，请确保您备份了您的数据！

在下面的示例中，我们将把/swapfile 中的可用交换空间从 4 GB 扩展到 8 GB。

*   关闭所有交换进程

```
sudo swapoff -a 
```

Enter fullscreen mode Exit fullscreen mode

*   调整交换的大小

```
sudo dd if=/dev/zero of=/swapfile bs=1G count=8 
```

Enter fullscreen mode Exit fullscreen mode

> if =输入文件
> 
> =输出文件
> 
> bs =块大小
> 
> count =块的乘数

*   更改权限

```
sudo chmod 600 /swapfile 
```

Enter fullscreen mode Exit fullscreen mode

*   使文件可用作交换文件

```
sudo mkswap /swapfile 
```

Enter fullscreen mode Exit fullscreen mode

*   激活交换文件

```
sudo swapon /swapfile 
```

Enter fullscreen mode Exit fullscreen mode

*   编辑/etc/fstab 并添加新的交换文件(如果它还不存在的话)

```
/swapfile none swap sw 0 0 
```

Enter fullscreen mode Exit fullscreen mode

*   检查可用的交换容量

```
grep SwapTotal /proc/meminfo 
```

Enter fullscreen mode Exit fullscreen mode

Ubuntu 18.04 或更新版本中的帖子[改变交换空间大小](https://bogdancornianu.com/change-swap-size-in-ubuntu/)最早出现在[波格丹一世·科尼安努](https://bogdancornianu.com)上。