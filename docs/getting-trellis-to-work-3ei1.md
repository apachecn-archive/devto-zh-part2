# 让 Trellis 发挥作用

> 原文：<https://dev.to/jj/getting-trellis-to-work-3ei1>

[![Trellis in Granada. We have that too](img/3f3028369017389d4806e7ee8d630ade.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U37IvgnQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cy0u89pq3zivpmn79hl.jpg)

Trellis 是一套基于流浪者和 Ansible 的脚本和工具，允许你轻松地建立一个**[Wordpress](https://wordpress.com)站点。**

 **然而，我碰到了几个问题。

## 问题 1: `vagrant up`不做任何事情

[`vagrant up`设置私钥](https://github.com/roots/trellis/issues/46#issuecomment-438390835)后停止。奇怪的是，这台机器已经被创建了，一个额外的`vagrant up`实际上启动了这台机器。但我对此无能为力。

*解决方案*:升级 VirtualBox。我有一个旧的 Ubuntu 版本。所以我去装了一个新版本，在 5.x 范围内。然而，下一个问题出现了。

## 问题 2: `virtualbox-5.2 : Entra en conflicto: virtualbox`

我从回购中卸载了 Virtualbox，得到了一个新的，添加了新的回购，但这个问题就是出现的问题。我删除了所有内容，清理了所有内容，什么都没做。

*解决方案*:在下载包并安装的时候，我最终得到了一个信息错误:有几个进程仍然从旧的 virtualbox 运行。终止进程，安装新版本的 VirtualBox。

然而，我们又回到了问题 1

## 问题 1 redux: `vagrant up`不做任何事情

完全一样的问题。怎么回事？

*解决方案*:不知何故，安装的机器有一些错误的安装。用`vagrant destroy default`(这是已安装机器的名称)销毁机器，然后重试。

然而，一个新的问题出现了。而且很奇怪。

## 问题 4: `in 'stat': No such file or directory @ rb_file_s_stat - /etc/exports (Errno::ENOENT)`

这个真的很毛。查了流浪版，是最新的。我真的想不出来

*解决方案*:谷歌一下错误，[这个问题](https://github.com/hashicorp/vagrant/issues/9970)出现了。而[这里是解决方案](https://github.com/hashicorp/vagrant/issues/9970#issuecomment-420240587):安装`nfs-kernel-server`。

我非常高兴。但是我又犯了一个错误:

## 问题 5: `name 'unicode' is not defined`还有 Ansible 发的很多。

因此...`Ansible failed to complete successfully`。

*解决方案*:这个很简单。每当 Python 错误中出现与 unicode 有关的东西时，它一定与 Python 版本有关。y 用`pyenv`来管理版本(你也应该这样)，所以我把全局版本改成了 2.x .问题解决了。

## 好了

基线是:我不确定 Trellis 的这些安装要求是否是最新的。我需要:

*   Virtualbox 5.2。它*肯定*不像说的那样与 4.3(我的是 4.4)一起工作。
*   流浪者 2.2
*   Python 2.x**