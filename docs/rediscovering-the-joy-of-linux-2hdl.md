# 重新发现 Linux 的乐趣*

> 原文：<https://dev.to/egimba/rediscovering-the-joy-of-linux-2hdl>

[![Masochist shaving yak dreaming of Linux](../Images/565dfae32a1cb6806e0d7a2895053b35.png)T2】](http://egmgem.com/blog/wp-content/uploads/2018/06/DSC_0001.jpg)

最近，我在一台麻烦的笔记本电脑上安装了 Ubuntu 18.04 和 Windows 10。最终一切都相对顺利。那是在我艰难地开始备份我在 Windows 上的文件之后。在安装 Ubuntu 之前，我不得不通过 LiveCD 这样做。那花了相当多的时间。硬盘可能有故障，所以必须尽快更换。一旦它启动并运行，就该安装我需要的各种软件应用程序了。

我发现 Cinelerra 并没有得到 Ubuntu 的本地支持，所以我需要想办法解决这个问题。据我所知，这是一个如此强大的工具(其网站上的用户视频样本显示了如此美丽的结果)，我不得不去寻找它。

我很惊讶地看到，搅拌机是支持的。我真的不应该，因为它是开源的，但我猜在 Windows 中使用它让我忘记了它的开源起源。

手刹也支持！我不认为我会知道。有一次，我在寻找一种在 Windows 中转换视频文件的合适方法时知道了这个方法。

我忘记了 GIMP 本身并不打开原始文件，所以在寻找解决方案时，我找到了 RawTherapee。不过，我还没有玩过。一旦我这样做了，一定要写下我的想法。我知道 Ufraw 是一个原始文件编辑器，但是经过研究，我发现 RawTherapee 是一个更好的选择。

digiKam 将取代 Lightroom。据我所知，这是一个非常强大的应用。有一本 digiKam 食谱书，我很快就会拿到，它可以帮助我学习软件。正如其作者 Dmitri Popov 所指出的，实际操作项目，尽管是菜谱格式，是学习软件的更好和更有效的方法，而不是逐个浏览每个菜单选项。我早该离开炼狱教程了！**

我安装的一些其他应用程序是 Chrome、Libre Writer/Calc、Filezilla、Git、Geany(在 Dev.to 上阅读了关于它作为 Windows 的 Notepad++的一个可能的替代品)和 Audacity。

我已经习惯了 Windows 中的其他应用程序，我需要 linux 的替代品。当需要使用它们时，我将记录获取它们的过程。我知道我需要在佳能 PIXMA 上为文档和光盘设置无线打印。初步研究表明我可以做到。我只需要坐下来详细了解一下。

我想做没有手机的 Whatsapp/insta gram/其他 app。试图解决这个问题是一次冒险。在切换到 Linux 之前，我曾经遇到过一个网站，它有一个名为 Franz 的应用程序，可以让一个人在一个屋檐下拥有几个消息应用程序。在我下载并安装它之前，我一直以为我已经挖到了金子。它不是一个允许我运行原生 Whatsapp 应用程序的模拟器，而是一个使用 web Whatsapp 的包装器，这意味着我需要一个实际的手机来扫描条形码，以允许它运行我的帐户。很明显，这对我没用。

在进一步搜索后，我找到了一个名为 TechViola 的网站，上面列出了七款可以让我在本地运行 Whatsapp 的应用。有些应用程序处于 alpha 阶段，有些处于 beta 阶段，有些是付费的，有些是免费的。我试了一眼最简单的(Andy OS)，结果发现这是一个 Windows 独有的选项。接下来是通过 Chrome(弧焊机)上的扩展，但未能启动应用程序。接下来，我试着安装了一个盒子，遇到了安装问题。他们说这是在阿尔法模式，所以我不应该太惊讶。唯一的问题是，把它弄下来是个问题。安装挂起，因此无法用 apt 做任何其他事情。每次我这样做时，都会被要求运行该命令

```
sudo dpkg --configure -a
```

收拾残局。然后又挂了起来。运转

```
sudo apt update
sudo apt upgrade
```

也一无所获。在研究中，我意识到我需要删除 */usr/src* 中的两个 anbox 模块，因为每次我试图更新时，apt 都会查看那里有哪些源代码可以处理。我最终手动删除了这两个令人不快的来源。然后跑了

```
sudo dpkg --purge -a
```

清除任何未决的配置问题。然后我跑了

```
sudo apt update
```

以清除配置。最后

```
sudo apt upgrade
```

顺利执行，没有任何错误。

在这个过程中，我用一个锁文件和命令 *ps* 、 *grep* 和 *kill* 重新授权。每次我在挂起后尝试使用 apt 时，我都会得到一条消息，表明/var/lib/dpkg/lock 文件被一个进程持有。我怎样才能找到那个过程呢？好吧，运行命令

```
ps aux | grep dpkg
```

这列出了令人不快的过程。然后只需运行命令

```
kill xxx
```

其中 xxx 是进程号。

回到模拟器...

然后我决定继续尝试 Genymotion。我按照文档网站上给出的安装说明进行了操作。我遇到了这样的问题，需要的虚拟盒子声明没有安装内核驱动程序。然而，在检查并看到它出现在应用程序菜单中时，我认为这对 Genymotion 来说已经足够了。所以我试着启动应用程序，遇到了一个错误，说它需要 *vboxdrv* 模块。所以我仍然需要弄清楚如何安装那个模块。

四处搜索后，我看到一个论坛帖子，建议删除 virtualbox

```
sudo apt-get remove virtualbox-\*
```

然后从 VirtualBox.org 下载软件包并手动安装。

```
sudo dpkg -i ./virtualbox-5.2_5.2.8-121009~Ubuntu~xenial_amd64.deb
```

我这样做了，然后遇到了依赖问题。安装过程说明需要 *libcurl4* 。我运行了命令

```
sudo apt install libcurl4
```

然后重复 virtualbox 安装命令。之后安装得很好。

然后我回到 Genymotion，执行它，它启动时没有任何问题。

下一步是设置虚拟设备。我选的是运行 Android 8.0 的定制平板。不幸的是，Whatsapp 与那台设备不兼容，所以我不得不选择另一台。我选择了三星 Galaxy 8。同样的问题！发生了什么事？回到寻找解决方案。在某个时候，我决定放回定制平板电脑，并尝试安装 Instagram。它安装没有任何大惊小怪！只是为了搞笑，我又试着安装了 Whatsapp。是的，这表明它是兼容的，安装也没有任何问题！这个我不懂。下次我没事做的时候，我会做一些调查来找出问题所在。但是现在，应用程序已经启动并运行了。我是一个快乐的露营者！

没那么快！第二天，当我试图启动 Genymotion 时，它失败了！原因？所需的 Virtualbox 模块没有加载！在查看 */dev* 文件夹时，那些模块不见了！返回故障排除模式...

在“应用程序”选项卡中，Virtualbox 显示为已安装。我真的可以运行它。然而，它确实发出了一个警告，称字符设备 */dev/vboxdrv* 不存在，声明没有它虚拟机将无法启动。

我回到 Virtualbox 论坛的说明，又试了一次。在这个过程中，我遇到了一个错误，表明插入 *vboxdrv* 失败。是时候使用另一个几乎被遗忘的指令来解决问题了: *dmesg* 。然而，运行它，没有产生任何线索。重读构建过程中的错误消息，它显示在执行 *vboxdrv.sh* 脚本时有一个错误。浏览脚本文件，我什么也挑不出来。然后，在思考这个问题的时候，我记得昨天在我找到的研究这个问题的众多网站中的一个网站上读到过，要成功安装 Virtualbox，需要禁用安全引导。我认为一旦安装，然后我可以重新启用它。果然，在我再次禁用它之后，一切都正常了。但是，我不能让它不安全，所以我去看看我是否可以操作 Virtualbox，同时仍然启用安全引导。

我找到的 yvind Stegard 的资源非常清楚地解决了这个问题。他在他的博客中解释说，vbox 模块没有被加载，因为它们是被指定的。Ubuntu 不签署第三方模块，建议关闭安全引导以允许它们。然而，由于这不是一个选项，他提供了一种方法来对它们进行签名，这样它们就可以像任何其他打开了安全引导的模块一样被加载。他的指令非常清楚，在实现后的测试中，我发现(使用 *lsmod | grep vbox* )模块确实被加载了。当然，Genymotion 启动时没有任何问题。我只需要记得在每次内核更新时给模块签名。也许我可以找到一种方法来自动化这个过程，这样一旦检测到更新，签名就会被激活。嗯（表示踌躇等）...

我知道我发泄了牦牛毛如何从窗户追我。然而，现在我在 Linux 中做着同样的事情。也就是说，我认为在 Linux 领域里剃牦牛毛比在 Windows 领域里剃牦牛毛要好得多，也更令人兴奋。在这个过程中，我学到了更多！

这一切有两点启示...

1.  为什么我愿意在虐待的窗户和蓝袜子关系中呆这么久？为什么？对于 Windows 部分，我早就知道有更好的选择！Bluestacks，我一直有一个暗示，一定有更好的东西，但研究，整理问题和安装一个替代品的麻烦似乎比它给我带来的虐待更成问题。啊，这种受虐狂，再也不会有了！

2.  我真的应该坚持摆弄内核和 Linux 的其他内部东西。如果我继续沿着这条路走下去，今天我会走多远？有一天，我突然意识到，在经过无数次定制后，我不仅能够自己编译 linux 内核，而且还能够进入实际的源代码，并更改那些似乎无法在我的机器上正常工作的东西。这个，成功建立 Gentoo 之后，然后后来，Arch。今天我几乎不能告诉你下载源代码的第一件事，更不用说配置了！是时候回到那个空间了，这次，永远地！就这一点而言，我想我会为了这次实验而让那台奄奄一息的笔记本电脑复活。

这些编年史是记录我在整个 Linux 生态系统中做各种事情的步骤的一种方式，以此提醒未来的自己如何处理我过去可能遇到的问题。发帖的频率可能多到一天几篇，也可能少到几个月一篇。

参考资料、链接和注释:

*   确实有一本书叫这个名字。做检查吧[https://www.goodreads.com/book/show/1871619.Linux 之乐](https://www.goodreads.com/book/show/1871619.The_Joy_of_Linux)

乌班图:[https://www.ubuntu.com/](https://www.ubuntu.com/)

cinelerra:[http://cinelerra.org/](http://cinelerra.org/)

blender:[https://www.blender.org/](https://www.blender.org/)

手刹:[https://handbrake.fr/](https://handbrake.fr/)

GIMP:[https://www.gimp.org/](https://www.gimp.org/)

被治疗者:[http://rawtherapee.com/](http://rawtherapee.com/)

幼发拉底:t0[【http://幼发拉底. sourceforge.net/](http://ufraw.sourceforge.net/)

digiKam:[https://www.digikam.org/](https://www.digikam.org/)

digiKam 食谱书:[https://www.digikam.org/recipes_book/](https://www.digikam.org/recipes_book/)

**教程炼狱[https://medium . freecodecamp . org/how-to-escape-Tutorial-炼狱-as-a-new-developer-or-at-any-time-in-your-career-e3a 4b 2384 a40](https://medium.freecodecamp.org/how-to-escape-tutorial-purgatory-as-a-new-developer-or-at-any-time-in-your-career-e3a4b2384a40)

弗朗兹:[https://meetfranz.com/](https://meetfranz.com/)

TechViola 文章:[https://www . tech viola . com/2018/04/Android-emulator-Ubuntu . html](https://www.techviola.com/2018/04/android-emulator-ubuntu.html)

求 Ubuntu 论坛说明如何解决文件在使用中被锁定的问题:[https://askubuntu . com/questions/15433/unable-to-lock-the-administration-directory-var-lib-dpkg-is-another-process](https://askubuntu.com/questions/15433/unable-to-lock-the-administration-directory-var-lib-dpkg-is-another-process)

genymotion:[https://www.genymotion.com/](https://www.genymotion.com/)

法比安·李关于安装 genymotion [的帖子 https://Fabian Lee . org/2017/09/23/Ubuntu-installing-the-genymotion-Android-emulator/](https://fabianlee.org/2017/09/23/ubuntu-installing-the-genymotion-android-emulator/)

VirtualBox 论坛:[https://forums.virtualbox.org/viewtopic.php?f=7&amp；t=87335 &放大器；p=422615#p422615](https://forums.virtualbox.org/viewtopic.php?f=7&t=87335&p=422615#p422615)

yvind Stegard 关于第三方模块签名的文章[https://ste gard . net/2016/10/virtualbox-secure-boot-Ubuntu-fail/](https://stegard.net/2016/10/virtualbox-secure-boot-ubuntu-fail/)

牦牛毛:【麻省理工学院人工智能实验室，2000 年后:原创。可能来自任&史汀普剧集。]任何看似无意义的活动，实际上是解决一个问题所必需的，而这个问题解决了一个问题，这个问题在几层递归之后，解决了你正在处理的实际问题。[http://www.catb.org/~esr/jargon/html/Y/yak-shaving.html](http://www.catb.org/%7Eesr/jargon/html/Y/yak-shaving.html)