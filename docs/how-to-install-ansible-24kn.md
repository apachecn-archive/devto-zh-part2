# 如何安装 Ansible

> 原文：<https://dev.to/craicoverflow/how-to-install-ansible-24kn>

原帖:[https://endaphelan . me/programming/guides/ansi ble/installing-ansi ble](https://endaphelan.me/programming/guides/ansible/installing-ansible)

Ansible 兼容任何运行 Linux 或 MacOS 的机器(抱歉 Windows 用户！).在继续阅读本指南之前，您需要安装 Python 2(版本 2.7 以上)或 Python 3(版本 3.5 以上)。

安装 Ansible 最简单的方法是使用操作系统的默认包管理器。

## 软呢帽

```
$ sudo dnf install ansible 
```

## RHEL/CentOS

```
$ sudo yum install ansible 
```

## Ubuntu

将 PPA 添加到您的系统:

```
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible 
```

安装 Ansible:

```
$ sudo apt-get install ansible 
```

## 德边

将 PPA 添加到您的系统:

```
$ echo "deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main" >> /etc/apt/sources.list 
```

现在将 PPA 添加到可信来源列表中，并更新系统:

```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
$ sudo apt-get update 
```

安装 Ansible:

```
$ sudo apt-get install ansible 
```

## MacOS

有关如何在 MacOS 上安装 Ansible 的说明，请参见下面的[使用 pip 安装](#installing-with-pip)。

## Arch Linux

```
$ sudo pacman -S ansible 
```

## 用 pip 安装

`pip`是 Python 的包管理器。我们需要安装才能使用它。

### MacOS

```
$ sudo easy_install pip 
```

### 软呢帽

#### Python 2.x

```
$ sudo dnf upgrade python-setuptools
$ sudo dnf install python-pip python-wheel 
```

#### Python 3.x

```
$ sudo dnf install python3 python3-wheel 
```

### RHEL/CentOS

#### Python 2.x

```
$ sudo yum upgrade python-setuptools
$ sudo yum install python-pip python-wheel 
```

#### Python 3.x

```
sudo yum install python3 python3-wheel 
```

### Ubuntu

#### Python 2.x

```
$ sudo apt-get install python-pip 
```

#### Python 3.x

```
$ sudo apt-get install python3-pip 
```

### Arch Linux

#### Python 2.x

```
$ sudo pacman -S python2-pip 
```

#### Python 3.x

```
$ sudo pacman -S python-pip 
```

一旦安装了`pip`，你就可以安装 Ansible:

```
$ pip install ansible --user 
```

## 安装 Ansible 的特定版本

如果您正在安装 Ansible 的最新版本，那么建议使用您的操作系统的默认软件包管理器。

有时我们需要安装旧版本。为此我们也可以使用`pip` :

```
$ pip install ansible==2.6 --user 
```

## 安装多个版本

我们可以使用`virtualenv`来创建和管理多个隔离的 Python 环境。这可以使用`pip` :
进行安装

```
$ pip install virtualenv --user 
```

为 Ansible 2.6 创建虚拟环境:

```
$ virtualenv ansible2.6 
```

激活`ansible2.6`环境:

```
$ source ansible2.6/bin/activate 
```

现在在虚拟环境中安装 ansi ble 2.6:

```
$ pip install ansible==2.6 
```

要停用环境，只需运行`deactivate` :

```
$ deactivate 
```

我们可以重复上面的步骤为 Ansible 2.7 创建另一个虚拟环境:

```
$ virtualenv ansible2.7
$ source ansible2.7/bin/activate
$ pip install ansible==2.7 
```