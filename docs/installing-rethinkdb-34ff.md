# 安装 RethinkDB

> 原文：<https://dev.to/ulisesac4/installing-rethinkdb-34ff>

## 简介

RethinkDB 是无模式 NoSQL 系统家族的一个数据库引擎。它被称为实时网络的数据库，因为它允许你订阅一个数据源，所以每次你对数据库进行更改时，你都会得到一个带有新数据的光标。它使用 JSON 数据像 MongoDB 一样存储信息。

在本教程中，您将在您的一台服务器上安装 RethinkDB。

## 要求

拥有一台运行以下操作系统的服务器:Centos、Debian、Fedora、Ubuntu。

## 安装

RethinkDB 不在最后一部分列出的发行版的官方回购中。所以你必须为你正在使用的匹配平台添加回购。请记住，您可以从源代码构建它，然后安装它，但从长远来看，这可能会成为一种负担。

### 厘斯

要在 Centos 中添加 repo，只需复制并粘贴下一个命令，注意 OS_VERSION，因为它必须与您使用的 Centos 版本相匹配。目前支持的版本是 6 和 7:

```
sudo wget http://download.rethinkdb.com/centos/OS_VERSION/`uname -m`/rethinkdb.repo -O /etc/yum.repos.d/rethinkdb.repo 
```

Enter fullscreen mode Exit fullscreen mode

然后可以用 yum:
安装

```
sudo yum install rethinkdb 
```

Enter fullscreen mode Exit fullscreen mode

### 德边

对于 Debian，一定要使用 Wheezy 或 Jessie 发行版，因为二进制文件是为它们构建的。然后你可以执行两个命令:

```
echo "deb http://download.rethinkdb.com/apt `lsb_release -cs` main" | sudo tee /etc/apt/sources.list.d/rethinkdb.list
wget -qO- https://download.rethinkdb.com/apt/pubkey.gpg | sudo apt-key add - 
```

Enter fullscreen mode Exit fullscreen mode

然后更新您的 repos 并安装软件包:

sudo apt-get 更新&& apt-get 安装 rethinkdb

### 软呢帽

没有为 Fedora 构建的官方包，但 Centos 包工作正常，建议使用 Centos 6 repo:

```
sudo wget http://download.rethinkdb.com/centos/6/`uname -m`/rethinkdb.repo -O /etc/yum.repos.d/rethinkdb.repo 
```

Enter fullscreen mode Exit fullscreen mode

现在事后用 yum:

```
sudo yum install rethinkdb 
```

Enter fullscreen mode Exit fullscreen mode

### Ubuntu

只要你的系统版本是 12.04 以上，就可以在 Ubuntu 中安装 RethinkDB。在 Debian 版本中，执行两个命令:

```
source /etc/lsb-release && echo "deb http://download.rethinkdb.com/apt $DISTRIB_CODENAME main" | sudo tee /etc/apt/sources.list.d/rethinkdb.list
wget -qO- https://download.rethinkdb.com/apt/pubkey.gpg | sudo apt-key add - 
```

Enter fullscreen mode Exit fullscreen mode

最后更新 repos 并安装包:

```
sudo apt-get update && apt-get install rethinkdb 
```

Enter fullscreen mode Exit fullscreen mode

## 启动服务器

几乎所有的管理任务都将通过 web 面板完成，要访问它，您只需进入`localhost:8080`，但是 RethinkDB 在安装后不会自动启动。您必须使用`rethinkdb`来启动它，您将在命令行中打开会话。您可以使用`rethinkdb --bind all`将它暴露给所有的网络流量，这样您就可以从您的远程主机访问它，但是请记住，这是不安全的，因为面板将对互联网开放。

## 跟进

安装 RethinkDB 就像启动发行版一样简单，启动服务也很容易。但是这个服务不是持久的，在本教程之后，你应该去阅读如何配置一个 systemd 单元服务给`enable`和`start`一个 RethinkDB 安装。