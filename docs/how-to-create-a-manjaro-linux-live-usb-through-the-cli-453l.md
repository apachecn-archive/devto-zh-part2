# 如何通过 CLI 创建 Manjaro Linux live USB

> 原文：<https://dev.to/lobo_tuerto/how-to-create-a-manjaro-linux-live-usb-through-the-cli-453l>

* * *

你可以在 [lobotuerto 的笔记查看这篇文章的最新更新版本——如何通过 CLI 创建一个 Manjaro Linux live USB。](https://lobotuerto.com/blog/how-to-create-a-manjaro-linux-live-usb-through-the-cli/)

* * *

# 下载 Manjaro i3 社区版

说到 Manjaro，我更喜欢 T2 i3 社区版。

请务必准备好一个`.iso`文件，或者从这里的[下载最新的图像。](https://manjaro.org/community-editions/)

# 用 SHA1 或 SHA256 验证

下载完`.iso`文件后，您还可以下载`.sha1`或`.sha256`文件。

要对照它们验证 **ISO** ，您可以这样做:

```
sha1sum -c manjaro-i3-17.1.12-stable-x86_64.iso.sha1
sha256sum -c manjaro-i3-17.1.12-stable-x86_64.iso.sha256 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，你会看到:`manjaro-i3-17.1.12-stable-x86_64.iso: OK`对他们俩来说。

# 为您的设备找到正确的字母

将记忆棒插入一个端口，用这个获得一个已连接设备的列表:

```
sudo fdisk -l 
```

Enter fullscreen mode Exit fullscreen mode

在输出中识别您的驱动器的字母，在我的例子中，我要寻找的驱动器是这个:

```
Disk /dev/sdc: 14.3 GiB, 15376318464 bytes, 30031872 sectors 
```

Enter fullscreen mode Exit fullscreen mode

我能看出来，因为它是一个 16GB 的设备，看看上面报道的 **14.3 GiB** 大小。

在本例中，驱动器的字母是来自`sdc`的`c`。可能是`b`、`d`或者别的什么。只需**确保**它确实是您想要丢弃的驱动器。一旦它被归零，你将无法恢复任何东西。

# 将 u 盘中的位清零

接下来，让我们将驱动器清零，这是一种安全措施，也可以让您判断驱动器是否仍然工作正常——如果驱动器正在死亡，这个过程将花费异常长的时间，或者它会在到达终点之前被卡住，这是您需要获得新 USB 的信号。

好，让我们用
在 u 盘上写满零

```
sudo dd if=/dev/zero of=/dev/sdc bs=4M status=progress conv=fdatasync 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**记住上面的命令是考虑到 USB 位于:`/dev/sdc`。

确保你有适合自己的正确道路！

该命令将向您报告进度。

# 将 ISO 镜像复制到 u 盘

完成后，转到下载**的目录。iso** 文件并将其部署到 USB 驱动器，如下所示:

```
sudo dd if=manjaro-i3-17.1.12-stable-x86_64.iso of=/dev/sdc bs=4M \
status=progress conv=fdatasync 
```

Enter fullscreen mode Exit fullscreen mode

就这样，现在你应该有一个可启动的 USB，里面有 **Manjaro Linux** ！