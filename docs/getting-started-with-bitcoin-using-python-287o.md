# 使用 Python 开始使用比特币

> 原文：<https://dev.to/harishkgarg/getting-started-with-bitcoin-using-python-287o>

## 简介

在本教程中，我们将使用 Python 来介绍比特币。我们
将使用 Python 的 [*比特币*](https://pypi.python.org/pypi/bitcoin)T5】库，方便地称为比特币。

## 先决条件

要开始使用 Python 使用比特币，我们需要，

*   可以运行 Python 编程环境的计算机

*   Python 或其他脚本语言的基础知识

*   从命令行程序运行命令和程序的能力

## 设置您的电脑

### 安装 Python

从 http://www.python.org/的
[*下载并安装 python 确保下载
Python 3 . x，因为这是我们将在本
教程中使用的。*](http://www.python.org/)

### 安装比特币 python 库

安装完 python 后，打开你的命令行程序，执行下面的命令来安装比特币 Python 库

> pip 安装比特币

## 你好比特币——生成私钥

我们将从用
Python 写一个相当于比特币的“Hello World”开始。要编写 python，你需要一个代码或文本编辑器，它支持 ASCII 格式。你不能使用写字板的 MS Word 进行
这个。你可以使用记事本，但是我们推荐使用免费的代码编辑器[*Atom Code
Editor*](https://atom.io/)。

打开你最喜欢的编辑器，输入下面的代码。在这段代码中，我们
首先导入比特币库。然后我们使用 ***random_key*** 函数生成一个私有
密钥，然后我们在屏幕上显示
私有密钥。

> 来自比特币进口*
> 
> 我的私人密钥=随机密钥()
> 
> 打印(我的私人密钥)

将它保存为一个. py 文件，然后打开你的命令行程序，像这样运行上面的程序。

> python<program location="" and="" name=""></program>

这将打印出一个私钥。

## 生成公钥

接下来，我们生成一个公钥。我们通过将我们
生成的私钥传递给 ***privtopub*** 函数来实现这一点

> 来自比特币进口*
> 
> 我的私人密钥=随机密钥()
> 
> my _ public _ key = privtopub(my _ private _ key)
> 
> 打印(我的公钥

## 创建比特币地址

在前面的程序中，我们生成了私钥和公钥。现在，我们
要去真正的比特币部分。在下面的代码中，我们通过将生成的公钥传递给
***pubtoaddr*** 函数来生成一个
比特币地址。

> 来自比特币进口*
> 
> 我的私人密钥=随机密钥()
> 
> my _ public _ key = privtopub(my _ private _ key)
> 
> my _ bit coin _ address = pubtoaddr(my _ public _ key)
> 
> 打印(地址)

## 创建多签名地址

接下来，我们创建一个多重签名的比特币地址。多重签名
地址是与多个私钥相关联的地址。
因此，我们首先创建 3 个公钥和私钥。然后，我们通过将 3 个公钥传递给***MK _ multisig _ script***
函数来创建一个
多重签名。最后，将得到的多重签名传递给***script addr***
函数，以创建多重签名比特币地址。

> 来自比特币进口*
> 
> 我的私人密钥 1 =随机密钥()
> 
> print('私钥 1: ' + my_private_key1)
> 
> my _ public _ key 1 = privtopub(my _ private _ key 1)
> 
> print('公钥 1: ' + my_public_key1)
> 
> 我的私人密钥 2 =随机密钥()
> 
> 打印('私钥 2: ' + my_private_key2)
> 
> my _ public _ key 2 = privtopub(my _ private _ key 2)
> 
> print('公钥 2: ' + my_public_key2)
> 
> 我的私人密钥 3 =随机密钥()
> 
> 打印('私钥 3: ' + my_private_key3)
> 
> my _ public _ key 3 = privtopub(my _ private _ key 3)
> 
> print('公钥 3: ' + my_public_key3)
> 
> my _ multi _ SIG = MK _ multi SIG _ script(my _ private _ key 1，
> my_private_key2，my_private_key3，2，3)
> 
> 我的多重地址= scriptaddr(我的多重签名)
> 
> 打印('多地址:'+我的多地址)

## 查看地址交易历史

我们还可以查看预先存在的比特币地址的交易历史。我们通过传递一个有效的比特币地址给函数
***历史*** 来实现。

> 来自比特币进口*
> 
> 打印(历史记录(a _ vaid _ 比特币 _ 地址))

如果没有地址，可以从
[*区块链*](https://blockchain.info) 查一个。

## 结论

在本教程中，我们用 python 介绍了比特币。我们看到了如何用 python 为比特币设置
。我们看到了如何生成私钥、
公钥和一个比特币地址。我们还看到了如何创建一个多
签名的比特币地址，以及如何查看一个
比特币地址的交易历史。

如果你想用 Python 更深入地研究比特币和区块链概念，可以看看我在 Packt 上的[视频课程](https://www.packtpub.com/application-development/getting-started-python-bitcoin-programming-video)