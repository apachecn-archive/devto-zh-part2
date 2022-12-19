# 系统和时间问题

> 原文：<https://dev.to/ferricoxide/systemd-and-timing-issues-27gh>

不像很多 Linux 人，我不是一个下意识地憎恨`systemd`的人。直到 2008 年，我的“受薪 UNIX”背景主要是 Solaris 和 AIX 等操作系统。特别是在 Solaris 上，由于 SMF，我已经习惯了使用`sytemd`类型的初始化系统。

也就是说，从 RHEL 和 CentOS 6 转换到 RHEL 和 CentOS 7 并不是没有问题。从`upstart`到`systemd`的变化比从`SysV-init`到`upstart`的变化要大得多。

`systemd`的大部分痛苦来自最初为 EL6 编写的 COTS 软件。一些供应商在说一些东西是 EL7 兼容的之前，真的只是相当粗略的测试。许多人——尤其是在 EL 7 生命周期的早期——根本不去创建`systemd`服务。他们只是依靠`systemd-sysv-generator`公用事业公司为他们干脏活。

虽然`systemd-sysv-generator`实用程序做了相当不错的工作，但是如果 legacy-init 脚本(托管在`/etc/rc.d/init.d`中的文件)实际上是指向文件系统中某个地方的符号链接，它就可能失败。即使这样，如果“其他地方”仍然在“`/`”文件系统中，也不是什么大问题。然而，如果您的 sop 包括“将操作系统和应用程序隔离到不同的文件系统上”，那么“其他地方”可能会成为一个很大的问题——当“其他地方”与“`/`”位于不同的文件系统上时。

最近，我被要求以“它在 EL6 上工作，所以它应该在 EL7 上工作”类型的“兼容性”来自动安装一些 COTS 软件。该软件不仅没有附带`systemd`服务文件，它的遗留初始化文件还链接到了安装在`/opt`中的软件。我们商店的 sop 是“在他们自己的文件系统上的应用程序”的种类。因此，`/opt/<APPLICATION>`目录实际上是托管在自己的存储设备上的自己的文件系统。完成安装后，我会重启系统。...当系统恢复时，即使`/etc/rc.d/init.d`中有一个引导脚本，服务也没有启动。仔细研究日志，我最终发现:

```
 systemd-sysv-generator[NNN]: stat() failed on /etc/rc.d/init.d/<script\_name>
 No such file or directory 
```

这让我觉得很奇怪，因为这个链接和它的目的地确实存在。

原来，`systemd`在系统初始化过程的早期就调用了`systemd-sysv-generator`实用程序。它很早就调用了它*，实际上，当/opt/文件系统运行时，它还没有挂载。因此，*在寻找进行转换的时候*符号链接指向的文件实际上*还不存在*。*

我的第一个想法是，“管它呢:我只要为这个愚蠢的应用程序写一个`systemd`服务文件就行了。”不幸的是，这个应用程序的开始有点像一个失败的老鼠窝；完全彻底的损失。试图通过`systemd`服务定义直接调用它会导致应用程序的打包控制器进程不知道在哪里找到它的子组件。易碎。所以，我寻找其他的选择...

最终，我的搜索引导我找到了关于`systemd`何时调用`systemd-sysv-generator`实用程序和[如何克服](https://access.redhat.com/solutions/3094591)的“到一个尚未挂载的文件系统的符号链接”问题。在启用了`systemd`的系统下，你可以在`/etc/fstab` — `x-initrd.mount`放置一个新的带`systemd`的挂载选项。您还需要确保您的文件系统的`fs_passno`设置为`0`...如果您的文件系统位于 LVM2 卷上，您需要更新 GRUB2 配置以确保 LVM 在`systemd`调用`systemd-sysv-generator`实用程序之前上线。闷热。

无论如何，一旦我实现了这个修复，`systemd-sysv-generator`实用程序对 sym 链接的 legacy-init 脚本感到满意...我的供应商的蹩脚的应用程序很高兴重启。

假设我在 AWS 上进行部署，我可以通过以下操作来设置这些 fstab 选项:

```
 mounts:
   - ["/dev/nvme1n1", "/opt/<application> , "auto", "defaults,x-initrd.mount", "0", "0"] 
```

在我的`cloud-init`声明块内。这应该适用于任何允许您使用`cloud-init`的环境。

我希望我可以说这是我在这个应用程序中遇到的最糟糕的问题。但是，实际上，这种应用是一种全方位的技术。