# 所以我昨晚搞乱了我的服务器

> 原文：<https://dev.to/huijing/so-i-mucked-up-my-server-last-night-2a0g>

世界上有两种人，喜欢命令行的人和不喜欢命令行的人。我属于前一类。因为我是一个 MS-DOS 婴儿(好吧，可能是 5 或 6 岁)，我开始了我的计算之旅，在那个可爱的白底黑字提示符下键入`dir/p`和`deltree`，试图弄清楚如何运行游戏。

<figcaption>啊，怀旧</figcaption>

[![MS-DOS prompt](../Images/af0d2ab2f69a8b15ff52d61100ae5612.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TG2tIEHp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/assets/images/posts/server-upgrade-fail/ms-dos.png)

附注:不要用`deltree`武装孩子，让他们无人监管。

一些人觉得奇怪的是，一个前端开发人员，一个 CSS 爱好者，喜欢摆弄服务器和系统管理员相关的活动。我也喜欢建造自己的机器，采购零件等等。我说，不要再把人分成小盒子了。

## 南下的 Ubuntu 升级

无论如何，我有一个自制的低功耗 NAS 在我的桌子下面运行，为我所有的媒体文件提供服务，自从我构建它以来，它一直运行 Ubuntu 16.04。但是现在 18.04 已经出来一段时间了，我想我昨晚应该升级一下。

事后看来，在晚上 9:30 跑步可能不是最好的主意，但我并不擅长做好决定。我的服务器的问题是，我有这个长期存在的问题与 *mdadm* 软件包被破坏，但我不能清除或重新安装它。

```
Setting up mdadm (3.3-2ubuntu7.1) ...
dpkg: error processing package mdadm (--configure):
subprocess installed post-installation script returned error exit status 20
Errors were encountered while processing:
 mdadm
E: Sub-process /usr/bin/dpkg returned an error code (1) 
```

Enter fullscreen mode Exit fullscreen mode

堆栈溢出试图帮助，但无济于事，但鉴于我仍然可以从服务器上提供文件，我已经离开了它。两年多了。现在它又回来咬我的屁股，因为这个问题中断了升级过程。

我不确定到底发生了什么，但看起来升级进行了一半，但当我重新启动后运行`sudo apt-get update`和`sudo apt-get upgrade`时，有许多软件包需要更新，所以我认为升级完成了。

## 外壳问题

不幸的是，我的[鱼壳](https://fishshell.com/)出了问题，不知道到底发生了什么，因为我的[我的鱼](https://github.com/oh-my-fish/oh-my-fish)装置似乎也出了点问题。我认为删除并重新安装整个设置会有所帮助。

事后看来，如果我做得好的话，可能会的。但我显然没有。已经很晚了。我没在想。但是我没有通过`apt`移除包，而是移除了整个`/usr/bin/fish`文件夹**，而没有先将默认 shell 设置回 bash。**

然后，我退出了。🤦‍♀️

我有效地将自己锁在了服务器之外，因为系统陷入了登录循环，无法再通过 ssh 登录。幸运的是，我可以通过安全引导直接访问服务器并以 root 身份登录。

## 我对恢复模式的了解

当我看不到我的默认用户的主目录时，有一些轻微的恐慌。我认为整个用户不知何故被删除了，但当我试图重新创建用户时，它已经存在了。

运行`cut -d: -f1 /etc/passwd`显示我的用户还在，只是不知何故`/home`完全空了。我在*错误的假设*下，认为每个用户的主目录对*根目录*可见，但显然我错了。

只有当我试图以默认用户身份登录时，我才意识到删除`/usr/bin/fish`是一个糟糕的主意。解决方法是通过编辑`/etc/passwd`文件将默认用户的 shell 改回`bash`。

不幸的是，我遇到了这个错误:

```
cannot lock /etc/passwd; try again later. 
```

Enter fullscreen mode Exit fullscreen mode

ಠ_ಠ

好吧，再来一次谷歌地图。这次我带着命令离开了:

```
sudo mount -o remount, -rw /dev/sda1 
```

Enter fullscreen mode Exit fullscreen mode

当我运行时，实际上什么也没发生，我认为这是一件好事。因为当我试图在`/etc/passwd`文件中保存我的更改时，它成功了。

## 包装完毕

这篇文章更多的是写给我自己，而不是其他人，因为我很确定我会再次做类似的蠢事，所以当我不得不进行类似的恢复工作时，我会有所参考。

_ (茨) _ /

TL:DR，不要在半睡半醒的时候做系统管理员的事情。不好的事情可能会发生。

<figcaption>尽管遇到种种麻烦，服务器世界又一切顺利</figcaption>

[![Server running Ubuntu 18.04.1 LTS](../Images/446c018ce303b3a51651f7e4d91f5617.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-TWtB5w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.chenhuijing.com/assets/images/posts/server-upgrade-fail/ubuntu-640.png)