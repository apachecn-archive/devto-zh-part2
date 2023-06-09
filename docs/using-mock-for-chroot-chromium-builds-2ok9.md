# 对 chroot Chromium 构建使用模拟

> 原文：<https://dev.to/rsclarke/using-mock-for-chroot-chromium-builds-2ok9>

## 简介

在 Linux 上检验和构建 Chromium 的[安装指南](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md)为 Ubuntu 以外的发行版提供了一些指导。然而，用于隔离构建环境和安装依赖项的脚本依赖于 Ubuntu 或 Debian 发行版。

本文利用 [Mock](https://github.com/rpm-software-management/mock/wiki) 在 [Fedora](https://getfedora.org/) 上创建一个 chroot 环境，其中将安装构建依赖项和工具。构建、运行和测试 Chromium 可以在 chroot 环境中进行，以避免包冲突。

实际上，chroot 环境和所有依赖项都是由一个模拟配置文件定义的。还提供了一个简单的 bash 函数来执行 chroot 环境中的命令，它通常可以作为上述安装指南中列出的命令的前缀。或者，您可以键入`chrsh`,这将使您进入 chroot 中的 bash shell，并遵循最初的指南。尽管您不需要安装额外的构建依赖项，因为这些都是使用 Mock 来处理的。

如果你想直接进入，可以在 [chrsh](https://github.com/rsclarke/chrsh) repo 中找到这个文件。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [斯克拉克](https://github.com/rsclarke) / [克里斯赫](https://github.com/rsclarke/chrsh)

### Chromium 的模拟配置，用于在 chroot 环境中构建、测试和运行。

<article class="markdown-body entry-content container-lg" itemprop="text">

# chrsh

Chromium 的模拟配置，用于在 chroot 环境中构建、测试和运行。

这个 repo 提供了一个[模拟](https://github.com/rpm-software-management/mock)配置文件和 bash 函数来隔离 Chromium 构建环境并简化与。

## 设置

### 模拟的

```
sudo dnf install mock
sudo usermod -a -G mock [User name]
newgrp - 
```

### chrsh

```
git clone https://github.com/rsclarke/chrsh.git
cd chrsh
sudo cp chromium.cfg /etc/mock
cat .bashrc >> ~/.bashrc
source ~/.bashrc
mkdir ~/chromium
mock -r chromium init 
```

## 建筑铬

### 安装`depot_tools`并取出铬源

```
chrsh git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
chrsh "echo export PATH=\$PATH:\${HOME}/depot_tools >> ~/.bashrc"
chrsh cd chromium \&\& fetch --nohooks --no-history chromium
chrsh cd chromium/src \&\& gclient runhooks 
```

### 更新`/etc/mock/chromium.cfg`

在上面的初始设置之后，大部分时间你将在`chromium/src`目录中执行命令。因此，建议取消对`/etc/mock/chromium.cfg`中所示行的注释

```
# Additional arguments to supply to systemd-nspawn
config_opts['nspawn_args'] = [
        "--setenv=DISPLAY={}".format(os.getenv("DISPLAY"))
        "--chdir={}".format(_home)
# Uncomment the
```

…</article>

[View on GitHub](https://github.com/rsclarke/chrsh)

本文的其余部分将介绍 Mock、chrsh 的设置，并检查和构建 Chromium 源代码！

## 设置

### 嘲弄

按照模拟[维基](https://github.com/rpm-software-management/mock/wiki#download)的说法，设置相当简单。尽管您可能希望注意在将用户添加到`mock`组时出现的安全隐患。

```
sudo dnf install mock
sudo usermod -a -G mock [User name]
newgrp - 
```

Enter fullscreen mode Exit fullscreen mode

### chrsh

获取文件、`chromium.cfg`和`.bashrc`，或者克隆存储库。

```
git clone https://github.com/rsclarke/chrsh.git
cd chrsh 
```

Enter fullscreen mode Exit fullscreen mode

将`chromium.cfg`复制到`/etc/mock`，并将`.bashrc`的内容添加到您的文件中。

```
sudo cp chromium.cfg /etc/mock/
cat .bashrc >> ~/.bashrc
source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

现成的配置文件假设 Chromium 源代码可以在 chroot 环境之外访问。否则，您希望使用的所有开发工具和实用程序也必须安装在 chroot 环境中。chrsh 遵循指南，将 Chromium 源代码放在`~/chromium`中，并将它绑定到 chroot 中您的用户的主目录。

```
mkdir ~/chromium 
```

Enter fullscreen mode Exit fullscreen mode

最后，您可以创建 chroot 环境，它将安装所有必需的构建工具和依赖项。

```
mock -r chromium init 
```

Enter fullscreen mode Exit fullscreen mode

## 检出并构建铬

其余部分是对[指南](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md)的改编，但使用了`chrsh`。

### 安装`depot_tools`

```
chrsh git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
chrsh "echo export PATH=\$PATH:\${HOME}/depot_tools >> ~/.bashrc" 
```

Enter fullscreen mode Exit fullscreen mode

### 获取代码

```
chrsh cd chromium \&\& fetch --nohooks --no-history chromium
chrsh cd chromium/src \&\& gclient runhooks 
```

Enter fullscreen mode Exit fullscreen mode

* * *

##### 侧注:

从这一点开始，指南中的命令将从`chromium/src`目录执行。自然运行`chrsh cd chromium/src \&\& <cmd>`并不优雅。因此，您可以通过取消注释`/etc/mock/chromium.cfg`中相关的`--chdir`参数来启动这个目录中的 shell。本指南假设您会这样做。

此外，您可以独立执行`chrsh`来提供 bash shell，而不是执行指南中的每个命令。

* * *

### 建筑铬

由于`chromium`目录安装在 chroot 中的相同位置(在您的主目录中)，所以最好也安装`cd ~/chromium/src`。至少你可以在文件名上完成制表符！

```
cd ~/chromium/src

# Generate build targets
chrsh gn gen out/Default

# Compile Chromium
chrsh ninja -C out/Default chrome

# Run Chromium
chrsh out/Default/chrome 
```

Enter fullscreen mode Exit fullscreen mode

### 测试目标

单元测试也可以在 chroot 中编译和执行。下面的例子构建并运行`pdf_uinttests`。参数`--gtest_filter`可以用来选择测试用例。

```
chrsh ninja -C out/Default pdf_unittests
chrsh out/Default/pdf_unittests --gtest_filter="RangeSetTest.*" 
```

Enter fullscreen mode Exit fullscreen mode

### 更新铬源检出

理论上，`git rebase-update`可以在 chroot 之外执行。

```
chrsh git rebase-update
chrsh gclient sync 
```

Enter fullscreen mode Exit fullscreen mode

## 下一步的步骤和想法

希望模拟配置文件对您有用，同时开发 Chromium 和 chrsh 简化了与它的交互。请随意评论，以下是您可能希望考虑进一步设置您的环境或使用 Chromium 的一些想法。

### 进一步发展

Chromium Linux 开发页面提供了更多关于开放问题和贡献的信息。

### 创建编译数据库

许多 ide 利用编译数据库对您正在处理的项目进行丰富的编辑。存储库中的`generate_compdb.py`脚本可以帮助您创建这个。

```
chrsh ./tools/clang/scripts/generate_compdb.py -p out/Default > compile_commands.json 
```

Enter fullscreen mode Exit fullscreen mode