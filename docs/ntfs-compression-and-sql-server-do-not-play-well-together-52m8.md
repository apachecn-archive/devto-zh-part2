# NTFS 压缩和 SQL Server 不能很好地配合使用

> 原文：<https://dev.to/sheldonhull/ntfs-compression-and-sql-server-do-not-play-well-together-52m8>

试验

想要主动将位于`C:\`上默认路径中的数据库移动到辅助驱动器，因为它增长得相当快。

我没有意识到的是这次冒险将确保。

## 第一课

不要将 SQL Server 数据库移动到有人在驱动器级别设置了 NTFS 压缩的卷上。

## 第二课

下次复制数据库，而不是移动。因为我没有备份，这会让我焦虑的 dba 放心。在你评判我之前..这是一个面向开发的环境，而不是生产…免责声明完成

## 令人讨厌的错误和警告接踵而至

首先，如果您尝试装载数据库并且它已经被压缩，您将得到一条错误消息。因为我以前从来没有这样做过，所以我没有意识到我正在陷入的困境。它会告诉你，如果不把数据库标记为只读，你就不能挂载它，因为它是一个压缩文件。

好吧…那就去`file explorer > properties > advanced > uncheck compress` …对吧？

不…

```
Changing File Attributes 'E:\DATA\FancyTacos.mdf' The requested operation could not be completed due to a file system limitation` 
```

Enter fullscreen mode Exit fullscreen mode

我发现这条信息和最喜欢的一样有用。网络错误消息`object reference not found`当然是如此容易立即修复。

## 修罗

*   调出音量属性。取消选中压缩驱动器或
*   如果您真的需要这种压缩，那么请确保解压缩包含 SQL Server 文件的文件夹并应用。

由于我无法通过切换文件来修复这么大的文件(它有 100gb 以上)，所以我想保持简单，并尝试将数据库复制回原始驱动器，取消标记 archive 属性，然后复制回我已删除压缩的驱动器，看看这是否可行。虽然这听起来像一个典型的“it 人群”修复(你有没有试着再次打开和关闭它)，我想我会尝试一下。

…成功了。令人惊讶的是，它刚刚工作。

这里有一个有用的脚本，可以帮助您在需要一段时间的情况下上路。使用风险自担，并且请…总是有备份！# dontblamemeifyoudindbackthingsup # CowsayChangedMyLife

最后，在将数据库复制回您的驱动器后，重新安装数据库…