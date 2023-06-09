# 如何从源代码构建 Linux 内核

> 原文：<https://dev.to/davidjones18/how-to-build-the-linux-kernel-from-the-source-code-f75>

在这篇文章中，我将告诉你如何预构建 Linux 内核，可能很多人已经知道了这一点，但是我相信对于那些人来说，这些材料也是有用的..

从源头上收集 Linux 内核的原因一般都是一样的——获得最新版本的内核，紧急应用安全补丁，针对特定任务和特定硬件进行优化，以及希望参与内核的开发，甚至是以 QA 的角色。

重要！遵循本文中的说明可能会导致您的数据丢失。做好备份，记住你做任何事情都是自担风险。下面描述的所有内容都在 Ubuntu 14.04 LTS 版上进行了测试。但是在其他版本的 Ubuntu 以及其他 Linux 发行版上，差别应该是最小的。

配置加载器
/etc/default/grub 规则是这样的:

GRUB_DEFAULT = 0

# GRUB_HIDDEN_TIMEOUT = 10

# GRUB _ HIDDEN _ time out _ QUIET = true

GRUB _ time out = 10
GRUB _ DISTRIBUTOR =`lsb_release -i -s 2> / dev / null || echo Debian`
GRUB _ CMDLINE _ LINUX _ DEFAULT = " quiet splash "
GRUB _ CMDLINE _ LINUX = " "
编辑后我们说:

sudo update-grub
因此，在启动系统 10 秒钟之前，会提示您选择要引导的内核。如果你弄乱了内核配置，想从以前的版本启动，那就非常方便了！

我们放置依赖关系
我们至少需要以下包:

sudo apt-get install git gcc make BC fake root dpkg-dev \
libncurses 5-dev libssl-dev
在许多系统上，它们都已经存在了。

获取源代码
内核源代码可以从 kernel.org 主页上的链接下载。在撰写本文时，内核的最新稳定版本是 4.6.4:

wget[https://www . kernel . org/pub/Linux/kernel/v4 . x/Linux-4 . 6 . 4 . tar . xz](https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.6.4.tar.xz)
tar-xz-xvf Linux-4 . 6 . 4 . tar . xz
CD Linux-4 . 6 . 4
或者，如果你需要非常非常 svezhak，你可以直接从 Git 获取源代码:

# 镜像:【https://github.com/torvalds/linux】T2

git clone ' Git://Git . kernel . org/pub/SCM/linux/kernel/Git/' \
' Torvalds/Linux . Git '
CD Linux
根据在 Git 中没有找到 v4.6.4 标签这一事实来判断，Linux 内核版本完全是以压缩的 tar 归档文件的形式发布的。

如果你想用 Canonical 的补丁来组装一个内核，而不是一个普通的内核:

git 克隆 git://kernel . ubuntu . com/Ubuntu/Ubuntu-trusty . git
CD Ubuntu-trusty
git tag | less
git check out Ubuntu-lts-4 . 4 . 0-31.50 _ 14 . 04 . 1
以我的经验，如果使用 Ubuntu，可以放心使用香草内核。你不太可能对它有任何问题。

注意:有趣的是，从现有的相对流行的 Linux 发行版来看，没有自己补丁的内核似乎只使用 Gentoo、Slackware 和 Arch Linux。

不管怎样，现在你有了来源。

我们收集并安装内核
选择构建内核的选项:

如有必要，进行 menuconfig
更改设置，点击保存，然后退出。因此，将创建一个包含我们选择的参数的. config 文件。

更新内核时(反正你已经在用什么内核了？)采用当前内核的配置，并将新选项设置为默认值是很方便的:

zcat /proc/config.gz >。/.最后，我们收集:

make-j4 bindeb-pkg local version =-custom
nucleus 要花很长时间。在我的笔记本电脑上，组装花了 1 小时 15 分钟。然而，大部分时间都花在了用调试符号构建一个巨大的内核包上。可以通过在配置文件中注释掉 CONFIG_DEBUG_INFO 参数来禁用此程序包的程序集。请注意，这个包需要 SystemTap 和其他有用的工具。

除了内核本身，您还可以收集以下文档:

# 其他还有`make pdfdocs`和其他，参见`make help`

make html docs
chromium-browser Documentation/DocBook/index.html
在子目录中的程序集末尾，您会看到类似这样的内容:

Linux-固件-image-4 . 4 . 13-custom _ 4 . 4 . 13-custom-1 _ amd64 . deb
Linux-headers-4 . 4 . 13-custom-1 _ amd64 . deb
Linux-image-4 . 4 . 13-custom _ 4 . 4 . 13-custom-1 _ amd64 . deb
Linux-image-4 . 4 . 13-custom-dbg _ 4 . 4 . 13 重新启动后，查看 uname -a 命令的输出。我们确信我们真的装载了一个新的内核。如果新内核有问题，在引导装载程序中，我们只需选择系统以前引导的内核。在加载旧内核后，我们快速移除新内核的包，瞧，系统回到了之前的状态。

此外，加载新内核后，您可以在其上运行测试:

sudo make kselftest
sudo make kselftest-clean

恭喜你，现在你可以构建一个 Linux 内核了！我在迪拜的许多软件公司工作过，我惊讶地发现大多数程序员和开发人员都不知道 Linux 的这些方面。保持这篇文章，确定你需要它