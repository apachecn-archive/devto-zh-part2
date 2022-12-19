# 如何使用 S3FS 在 CentOS 和 Ubuntu 上安装 S3/Wasabi/数字海洋存储桶

> 原文：<https://dev.to/dotlayer/how-to-mount-s3wasabidigital-ocean-storage-bucket-on-centos-and-ubuntu-using-s3fs-l9j>

本文原载于 [Dotlayer](https://dotlayer.com/how-to-mount-s3-wasabi-digital-ocean-storage-bucket-on-centos-and-ubuntu-using-s3fs/) 。

在本文中，我们将使用 S3FS 在 CentOS 或 Ubuntu box 上本地挂载云存储服务。将它们安装在本地将允许我们作为本地文件系统与云提供商进行交互。

我们将涵盖三家云存储提供商，即 AWS S3、Wasabi Hot Storage 和 Digital Ocean Spaces。有趣的是，这三个提供商都使用相同的 api，即 S3 API。

S3FS 是一种基于 FUSE(用户空间中的文件系统)的解决方案，用于安装亚马逊 S3 或兼容的存储解决方案，正如上文所述，我们可以对该驱动器使用系统命令，就像系统中的另一个硬盘一样。在 s3fs 挂载文件系统上，我们可以简单地使用 cp、mv 和 ls 这些基本的 Unix 命令，类似于在本地连接的磁盘上运行。

# 步骤 1–更新软件包

首先，我们将更新系统，以确保我们有最新的软件包。然后，我们将检查您的系统上是否安装了任何现有的 s3fs 或 fuse 软件包。我们将删除任何现有的软件包，并重新安装一切。

### CentOS 和 RedHat 系统

$ yum 更新
$ yum 移除保险丝 fuse-s3fs

### Ubuntu 系统###

$ sudo apt-get 更新
$ sudo apt-get 移除保险丝

步骤 2–安装依赖项

成功移除软件包后。我们将安装 fuse 的所有依赖项。使用以下命令将所需的软件包安装到系统中。

### CentOS 和 RedHat 系统

yum install gcc libstdc++-devel gcc-c++ curl-devel libxml 2-devel OpenSSL-devel mailcap

### Ubuntu 系统###

sudo apt-get install build-essential lib curl 4-OpenSSL-dev libxml 2-dev mime-support

步骤 3–下载并编译最新的 s3f

在这一步我们将从 GitHub 下载并编译 s3fs 的最新版本。对于本文，我们使用的是 s3fs 版本 1.74。下载完成后，使用下面的命令在系统中解压文件并编译源代码。

cd /usr/src/
git 克隆[https://github.com/s3fs-fuse/s3fs-fuse.git](https://github.com/s3fs-fuse/s3fs-fuse.git)T3】CD s3fs-fuse
。/autogen.sh
。/configure
make&T8】make 安装

步骤 4–配置访问密钥

为了配置 s3fs，我们需要您的 S3 亚马逊帐户、Wasabi 或 Digital Ocean 的访问密钥和秘密密钥。您可以从各自服务的帐户仪表板中访问这些密钥。有了这些密钥后，创建一个文件，在本教程中，我们将把这个文件命名为 pwd-s3fs。

echo AWS _ ACCESS _ KEY _ ID:AWS _ SECRET _ ACCESS _ KEY > ~/。pwd-s3fs

### 注意:用你的实际键值改变 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY。

创建密码文件后，出于安全原因，我们将更改该文件的权限。

chmod 600 ~/。pwd-s3fs

步骤 5–安装 S3 铲斗

最后，我们可以使用下面的命令集来装载 s3 存储桶。对于这个例子，我们使用 s3 存储桶名称作为测试存储桶，挂载点作为/s3mnt。

mkdir/tmp/cache/s3mnt
chmod 777/tmp/cache/s3mnt
s3fs-o use _ cache =/tmp/cache test-bucket/s3mnt-o passwd _ file =/etc/pwd-s3fs

一旦成功运行，您就可以通过将目录更改为/s3mnt 来访问 test-bucket 中的所有文件。
步骤 6–安装芥末热储水桶

如果你不熟悉 Wasabi，根据他们的网站，这是一种 S3 兼容存储，价格是亚马逊 S3 的 1/5，速度是亚马逊的 6 倍。因为它支持 S3 API，所以相同的命令也能工作，您只需要按如下所述更改端点:

s3fs test-bucket/s3mnt-o passwd _ file =/etc/pwd-s3fs-o URL =[https://s3.wasabisys.com](https://s3.wasabisys.com)

步骤 7–建立数字空间

就像 Wasabi 的方法一样，我们将在命令中指定端点。使用下面的命令将数字海洋空间安装到您的本地驱动器上。

s3fs test-bucket/s3mnt-o passwd _ file =/etc/pwd-s3fs-o URL = NYC 3 . digital ocean spaces . com

最后

在本文中，我们回顾了如何使用 s3fs 在本地挂载云存储服务。通过这样做，我们可以使用常规的 linux 命令在本地与远程 bucket 中的文件进行交互。一些使用案例包括运行备份、复制文件等等。