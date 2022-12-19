# 如何激活访客添加

> 原文：<https://dev.to/nabbisen/virtualbox-how-to-activate-guest-additions-1ojg>

# 总结

这是一个关于 [VirtualBox](https://www.virtualbox.org) 的小技巧。
我们可以通过安装 VirtualBox 来宾附件来让 VirtualBox 变得更加有用。

这一扩展使我们能够体验无缝的鼠标移动，在主机操作系统和客户机之间共享剪贴板或文件，并动态配置屏幕分辨率。
[官方网站称](https://www.virtualbox.org/manual/ch04.html)它使我们能够:

*   鼠标指针集成
*   共享文件夹
*   更好的视频支持
*   无缝窗口
*   通用主/客通信通道
*   时间同步
*   共享剪贴板
*   自动登录(凭证通过)

# 操作流程

### 环境

*   主机操作系统: [Manjaro Linux](https://manjaro.org/) 基于 [Arch Linux](https://www.archlinux.org)
*   来宾操作系统:[拉斯比安](https://www.raspbian.org/)

### 第一步:获取安装程序

Manjaro Linux 软件包管理器列出了 virtualbox-guest-iso 软件包:

[![](../Images/94f42b84eb6658de0ffdce99cdbcde67.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xXnv_MWc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p5wygacbn6zkbbo9zzrs.jpg)

根据[archi wiki](https://wiki.archlinux.org/index.php/VirtualBox)，安装它会创建 ISO 文件为`/usr/lib/virtualbox/additions/VBoxGuestAdditions.iso`。

或者，我们也可以[直接下载](http://download.virtualbox.org/virtualbox/)。
(另外，事先检查一下 VirtualBox 版本也是有必要的。)

### 第二步:安装

将 iso 设置为来宾存储，如下所示:

[![](../Images/36af8e1306ab68f12b785fd014686759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qjmA7FLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9cnz8mi0kaisxj0ioi5v.png)

然后启动客户操作系统。

在 Raspbian 中，iso 媒体会自动挂载。
(否则需要手动挂载。)

将 VirtualBox Guest 附加组件安装到 Guest 虚拟机的脚本位于媒体目录中。

[![](../Images/c11c2f6281c6a8cc3d6098a861465bd5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aIJ9bE3t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/byz8h3chk6vtm9hloyuj.png)

选择与客户操作系统兼容的脚本并运行:

```
$  cd /media/cdrom1
$  sudo sh VBoxLinuxAdditions.run 
```

[![](../Images/60158f85315e7075aab02a698ad883b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--53HqjEpM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ma2vqmgh98uxvjjgiy0.png)

安装完成后，关闭客户机并从存储中弹出 iso。完成了。

# 结论

现在可以通过设置、菜单或页脚图标使用 VirtualBox 访客插件。

### 共享文件夹

我们可以在机器设置的“共享文件夹”菜单中注册主机/访客之间的共享文件夹(或在访客控制菜单:[设备]-[共享文件夹]):

[![](../Images/71fba8fe17fe90ccdcd214bdde0f0384.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n6g0OEap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f03i11d106p0u14frmo0.png)

### 共享剪贴板/更好的视频支持/等等

我们可以在主机/访客之间共享剪贴板，或者在访客运行时对页脚图标做一些事情。

页脚图标有多个菜单，如下所示:

[![](../Images/678783106f87e734903f8775e1e7ba93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6SBaAG79--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dsq6l07ampytw61t32ix.jpg)

我们也可以在一个客户操作系统中使用多个屏幕: )

[![](../Images/76956244a344f312e5eb78ec6619f3f2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JFG3Ksee--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uc06xni9zqb16vka9irv.jpg)