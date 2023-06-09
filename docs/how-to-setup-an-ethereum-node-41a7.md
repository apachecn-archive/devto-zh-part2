# 如何设置以太坊节点

> 原文：<https://dev.to/legobox/how-to-setup-an-ethereum-node-41a7>

[![ethereum homestead](img/3e415a3f5cc2506f05693ba6236cc57f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IyqKQK_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29kd8x7xuk6vavmvqd4r.jpg)

> 这篇文章最初发表在我的博客上，地址是 [legobox](https://medium.com/legobox/setting-up-an-ethereum-node-5193a98446e8)

## 以太坊节点是什么

随着比特币的出现，基于区块链的技术逐渐变得越来越受欢迎，使开发者能够构建去中心化的应用程序，今天许多人认为这是互联网上可能存在问题的数据控制的解决方案。

以太坊是独特的，因为它试图使用区块链的相同解决方案，允许任何人都能够公开 dapps(分散式应用程序)，并使它们能够在提供商和客户端之间直接连接。

在以太坊网络上，每个客户端都是一个节点，是更大网络的一部分，因此在设置客户端时，它必须下载整个网络的数据库和交易读数。在本文中，我们将回顾在 Ubuntu (Linux)服务器上设置以太坊节点的所有过程。

## 先决条件

在进入与设置节点相关的配置和命令之前，有几个基础知识是很重要的，因此您应该在一定程度上了解以下内容。

*   区块链技术及其工作原理。
*   在 geth 客户机上管理帐户(谢天谢地，我们将在文章中谈到这一点)

## 节点类型

网络上基本有两种类型的节点，

*   轻量级节点
*   完整节点

**轻量级节点**旨在运行在资源较低的机器上，因此保留区块链数据的一个非常浅的副本，而**全节点**跟踪网络上执行的所有事务，因此。

在我们的设置中，我们将回顾这两个系统，以便具体了解如何进行该过程。

## 设置 Geth 客户端(节点)

区块链以太坊有许多客户，但最安全和记录最完整的客户包括

*   薄雾钱包
*   geth wallet mist wallet 是一个基于 GUI(图形用户界面)的客户端，这使用户能够通过点击和所有操作来控制钱包，还可以在 mist wallet 上开发合同。

### 设置一个薄雾钱包

要设置 mist wallet，请导航至[以太网主页](https://etheruem.org)，然后进入下载部分，您将找到下载按钮，通过该按钮您可以下载新版本的 mist wallet。

这需要一些时间来设置，因为它必须下载区块链的数据库，在下载完成后，你的计算机实际上是以太坊网络上的一个节点。

#### 雾钱包上的建筑合同

一般来说，在 mist 网络或以太坊网络上建立契约是在 solidity 中完成的，它是一种强类型语言，具有 C++和 javascript 之间的某种对称性。在 mist wallet 上创建一个契约非常简单，只需切换到部署部分，将契约的代码放在那里，然后继续进行部署。你可以在这里找到一个很好的例子。

#### 设置 Geth 客户端钱包。

对于不同的操作系统，有不同的方法来设置 geth 客户端。

###### 为 Windows 设置

听说过 Chocolatey 包管理器吗，它提供了一种简单的方法来安装所需的构建设置和工具。设置和安装巧克力收银台[https://chocolatey.org](https://chocolatey.org)。

接下来，我们设置管理员命令提示符并安装我们需要的构建工具。

```
C:\Windows\system32> choco install git
C:\Windows\system32> choco install golang
C:\Windows\system32> choco install mingw 
```

安装这些包将设置 path 环境变量。

接下来，让我们确保安装了 Go

```
C:\Users\xxx> set "GOPATH=%USERPROFILE%"
C:\Users\xxx> set "Path=%USERPROFILE%\bin;%Path%"
C:\Users\xxx> setx GOPATH "%GOPATH%"
C:\Users\xxx> setx Path "%Path%"
C:\Users\xxx> mkdir src\github.com\ethereum
C:\Users\xxx> git clone https://github.com/ethereum/go-ethereum src\github.com\ethereum\go-ethereum
C:\Users\xxx> cd src\github.com\ethereum\go-ethereum
C:\Users\xxx> go get -u -v golang.org/x/net/context 
```

最后，命令编译 geth

```
C:\Users\xxx\src\github.com\ethereum\go-ethereum> go install -v ./cmd/... 
```

有了所有的设置，我们就可以很好地在 windows 上工作了。

###### 在 Linux (Debian)上设置

**从 ppa** 安装

```
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install ethereum 
```

如果你想保持领先，安装`ethereum-unstable`包。安装后，运行`geth account new`在您的节点上创建一个帐户。你现在应该可以运行`geth`并连接到网络。

还有其他选择，比如从源代码构建

**从源构建**

构建 Geth(命令行客户端)

*   将存储库克隆到您选择的目录中

```
git clone https://github.com/ethereum/go-ethereum 
```

*   如果您还没有最新版本的 Go(1.7 版)，请安装它
*   构建`geth`需要安装 Go 和 C 编译器。

```
sudo apt-get install -y build-essential golang 
```

*   使用以下命令继续构建

```
cd go-ethereum
make geth 
```

*   要启动节点，运行`build/bin/geth`

###### 在 Mac 上设置

入门 go-ethereum 最简单的方法就是运行 homebrew。要安装自制软件[,请查看此](http://brew.sh/)

接下来，添加 tap 并安装 geth

```
brew tap ethereum/ethereum
brew install ethereum 
```

## 管理账户

> 请务必记住您的密码

在 geth 上，通过账户命令
提供客户端账户管理

```
$ geth account <command> [options...] [arguments...] 
```

这允许您创建新帐户、列出现有帐户、将私钥导入新帐户、迁移到新的密钥格式以及更改密码。

我们将复习一些重要的命令。
**创建账户**

```
$ geth account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat Passphrase:
Address: {168sdunina2ee09042d83d7c5811b5334789349934} 
```

**清单账户**

```
$ geth account list
Account #0: {5afdd78bdacb56ab1dad28741ea2a0e47fe41331} keystore:///tmp/mykeystore/UTC--2017-04-28T08-46-27.437847599Z--5afdd78bdacb56ab1dad28741ea2a0e47fe41331
Account #1: {9acb9ff906641a434803efb474c96a837756287f} keystore:///tmp/mykeystore/UTC--2017-04-28T08-46-52.180688336Z--9acb9ff906641a434803efb474c96a837756287f 
```

**用自定义数据目录**
将私钥导入节点

```
$ geth account import --datadir /someOtherEthDataDir ./key.prv
The new account will be encrypted with a passphrase.
Please enter a passphrase now.
Passphrase:
Repeat Passphrase:
Address: {7f444580bfef4384839403049823453c9d} 
```

## 结论

对于许多人来说，区块链技术仍然相对较新，该领域正在发生很多事情，但对于构建 dapps 和使用分散设置来说，入门应该不是问题。在这种技术下的后续文章中，我将深入探讨可靠性的本质，以及如何着手构建和部署智能合约。