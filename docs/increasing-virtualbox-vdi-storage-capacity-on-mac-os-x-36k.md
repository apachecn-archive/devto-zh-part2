# 增加 Mac OS X 上的 VirtualBox VDI 存储容量

> 原文：<https://dev.to/akshaygoyal174/increasing-virtualbox-vdi-storage-capacity-on-mac-os-x-36k>

*原载于[媒体](https://medium.com/@akshay.g174_68301/increasing-virtualbox-vdi-capacity-on-mac-os-x-ac4d52b63f0c)T3】*

[![Resizing you Virtual Box storage is as simple as resizing this image, not really<br>
](img/c9f8641345fb2e0d7a19dcd5ffd1036f.png "Resizing you Virtual Box storage is as simple as resizing this image, not really")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A1pSgsSW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Abn4isgq4L6kLoIBEn0kqkg.png)

我在 Mac 上使用 VirtualBox 安装程序运行 Ubuntu 作为访客操作系统。这个设置帮助我在本地开发机器上模拟生产环境。VirtualBox 对于任何试图运行访客操作系统的人来说都是一个非常方便的软件。虽然有一个初始的学习曲线，但它的乐趣。我已经使用这个系统一年多了，它很适合我。嗯，嗯，直到今天…

今天，在开发一个传奇的(读起来像是遗留的)软件时，我需要下载最新的数据库转储。所以，像任何优秀的程序员一样，我试图下载转储文件，却发现我不能这样做，因为…我的虚拟机空间不足。我突然想起我给 VDI 分配 20 千兆字节空间的时候，心想我再也不需要比这更多的空间了。哈哈的笑..我错了吗！！。遵循 lazy dev 协议，我尝试了最快的解决方案；清理日志和删除大多数不需要的文件，但无济于事。终于到了我把手弄脏的时候了。几个搜索结果之后…

## 遵循的步骤

*   备份你的 VDI
    ([https://forums.virtualbox.org/viewtopic.php?f=2&t = 58803](https://forums.virtualbox.org/viewtopic.php?f=2&t=58803)

*   获取 VDI 的路径
    *保存此路径，我们将在接下来的步骤中使用它*
    右键单击虚拟机- >单击设置- >存储- >在左侧的存储树上选择您的 VDI->位置会有您的路径。植物草图索引（vegetation draught index 的缩写）

[![Path to the VDI is highlighted<br>
](img/090d50d310b049df18b5b410f03e4f5b.png "Path to the VDI is highlighted")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UXBSrWBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aigq15rMNn2aI61R5B903CA.png)

*   关闭虚拟机并退出 VirtualBox
*   导航到 VBoxManage 应用工具文件夹，打开终端应用程序，然后键入以下命令

```
cd /Applications/VirtualBox.app/Contents/Resources/VirtualBoxVM.app/Contents/MacOS/ 
```

Enter fullscreen mode Exit fullscreen mode

*   修改分配给 VDI 的大小使用上面的 VDI 路径，并在下面的命令中使用它

```
VBoxManage modifyhd — resize [new size in MB] [/path/to/vdi] 
```

Enter fullscreen mode Exit fullscreen mode

[![Is it over yet?](img/d1d08158a6dd676e622573fa4c7935c4.png "Is it over yet?")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DTe81iGc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AOfT1MxmR0MwJE9oYKufVZQ.png)

提示:注意空格前的“\”。这用于转义路径中的空格字符。

*   验证所需的更改是否成功

```
VBoxManage showhdinfo [/path/to/vdi] 
```

Enter fullscreen mode Exit fullscreen mode

[![Yay!!](img/0acd7c85104ece5a81f5c79556515810.png "Yay!!")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gF7bVOaz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9hcwL4UFmM4FensdSyG7gw.png)

*   启动 VirtualBox 和虚拟机

提示:您可能需要重新分配分区来使用新空间。

**注意:**我用的是 VirtualBox 5 . 1 . 14 版本。如果您使用的是较新的版本，您可能希望使用 modifymedium 而不是 modifyhd，尽管仍然支持 modifyhd。

*几个搜索结果之后……*

…我愉快地在我的虚拟机上下载数据库转储，同时完成了这篇博文。:)

如果你喜欢这篇文章，请随时在 Twitter 上关注我。您也可以访问我的 [Github](https://www.github.com/ninetyone) 页面，查看一些有趣的项目，并为它们做出贡献。

## 资源

*   https://www.virtualbox.org/manual/ch08.html
*   [[http://OSX daily . com/2015/04/07/how-to-resize-a-virtualbox-VDI-or-vhd-file-on-MAC-OS-x/](http://osxdaily.com/2015/04/07/how-to-resize-a-virtualbox-vdi-or-vhd-file-on-mac-os-x/)]