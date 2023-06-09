# AWS 和混乱的 Git 实践:SecDSM March MiniCTF 解决方案

> 原文：<https://dev.to/antoinette0x53/aws-and-messy-git-practices-secdsm-march-minictf-solution--1ngh>

*[最初贴在 antoinettestevens.com 的](https://antoinettestevens.com/secdsm-march-minictf-solution/)*

在失踪一个月后，我终于回到了 SecDSM。对于二月份的 MiniCTF，我没有解决方案，但是它涉及到机器学习、CAPTCHA 暴力破解和脚本编写。不幸的是，我正忙于准备 3 月 1 日的一个大型活动，没有时间专注于这个挑战，尽管我发现它真的很有趣。

三月 SecDSM MiniCTF 非常有趣，我了解了 AWS cli 以及确保使用最新版本工具的重要性。让我们一起来看看。我会尽最大努力来完成这个过程，并解释我在这个过程中的思维模式。我应该先说一下，我以前从未使用过 AWS。只看过一次，在一个安全会议上看到过一个演讲。

# 侦察

[![challenge](img/04ac13a30bd3d6fed23e611b76265f3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s7VnFHUn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1RGgd5u.png) 
访问 http[s]://作品集[。]xbcw[。]net 显示一个相当无害的网站。没有任何字段可以输入文本，所以我立即排除了我所知道的大约一半的网络攻击，主要是注入。唯一要做的就是开始浏览网站。我使用 Chrome 浏览器，所以我倾向于打开“Inspect”来浏览文件和网络连接。我尝试查看是否有 robots.txt，得到一个 XML 文件形式的错误。这对我来说似乎很奇怪，因为如果一个页面不存在，我通常只会得到一个正常的 404 页面。继续，我注意到顶部的图标是 LinkedIn(不去任何地方)、Github Repo 和 PDF 简历的链接。我看了看 PDF，没看出什么特别的地方。我决定开始检查 Github repo，因为我认为我能够找到更多关于已部署代码的信息，即使没有别的。

回购的自述文件没什么意思，所以我开始点击文件，却不知道自己在找什么，也不知道从哪里开始。几分钟后，我决定检查一下提交。我不能完全解释为什么，除了说我认为知道回购所有者更改或删除了什么会有所帮助，以防它给我一个提示，告诉我应该在网站上寻找什么。在这一点上，我还不知道是 AWS。

当我意识到这个项目的提交中隐藏着一些非常有趣的东西时，我仔细查看了回购和点开始连接。

我发现的第一件事是:
[![](img/ee7e784c98c262912d7e92d8d0d659f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XtKDujAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/PVtf9b5.png) 
现在，我不知道它是什么意思，但“桶”这个词是我知道我们是如何处理 AWS 的。在未来，我可能会更快地认出它，但是，在那一刻，它就是这么做的。_(ツ)_/

我一直听说亚马逊 S3 桶配置不当，容易受到攻击。这是开发人员之间的一个热门话题，所以我知道这可能是类似的东西。每当我觉得我对自己的目标有很强的把握时，我的下一步总是去谷歌一下。这让我找到了 Rhino Security Labs 的一篇文章，[渗透测试 AWS 存储:踢 S3 桶](https://rhinosecuritylabs.com/penetration-testing/penetration-testing-aws-storage/)。

我浏览了 S3 的 URL，发现 XML 列出了这个 bucket 的所有内容...包括一个名为 secrets.zip 的文件。

[![s3 bucket addr](img/9f1d3643410528a2563224817e825d6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iBmhfU4n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Lrw9lGb.png) 
[![](img/4bfa52049dd40a696538636deb51c875.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oSgddIRu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/QQmN8z4.png) 
和现在的我很像...

[![](img/cae081e0033afa31a5bda7ede5bf8db4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b_3M7zvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/itMcsy5.gif)

我对那个 zip 文件(你需要 pzip)做了一个 wget，发现它是有密码保护的。zip 上的`strings`会显示有一个名为 secrets.txt 的文件，但这里没有类似密码的东西。这就是我遇到困难的地方。

> 来自侦察的随机观察:
> 
> *   在这方面，我尝试了 Wireshark。直到我在过滤 IP 地址的时候，我才想起这是没有用的，因为这个网站是 HTTPS，而且流量是加密的。
> *   在查看代码时，我注意到网页上使用的图像名称被命名为“gotcha”之类的东西，以防人们认为这些图像是我假设的[隐写](https://en.wikipedia.org/wiki/Steganography)挑战。#利用(？)

很多人面对密码提示时的第一反应是强行输入。在我见过的所有 CTF 中(实际上没有那么多)，如果他们想让你使用暴力，他们通常会做两件事情中的一件。他们会给你提供(或者藏在某个地方让你找到)一个词汇表，或者对一个流行的词汇表做一些双关语。否则，蛮力通常不是答案。查看 Better Buys 的这篇文章，了解尝试盲目暴力破解密码所花费的时间。

回顾我为 lambda-upload-portfolio.py 找到的 commit，有一条评论提到密码“将在文件名下的参数存储中”。我不知道这是什么意思，所以我再次搜索，最终在 Medium 上找到了一篇[文章，解释了如何访问参数存储库](https://medium.com/@mda590/simple-secrets-management-via-aws-ec2-parameter-store-737477e19450)。这正是我所需要的。我进入 AWS CLI(用 apt-get 安装在这个过程中的某个地方),我得到一个错误，说我必须先运行`aws configure`。`aws configure`命令要求我知道 S3 存储桶的访问密钥 ID 和访问密钥秘密。*井屎*。我不知道，也不知道如何找到它。在几分钟的碰壁之后，我回头看看 Git 提交，心想也许我错过了什么。然后...
[![](img/a40882c97da02943f0a061dc31fb974b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QIfBk66F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/y8pTJl8.png)

这就是为什么当你提交给一个公共库时，你应该总是非常小心。哦好吧。现在我可以配置 AWS CLI 并做我需要做的事情。我按照 Medium 博客的指示，遇到了一个问题。`get-parameters`不见了。它不存在。我想我一定是做错了什么，尝试了其他事情，但运气不好。最终其他人在我之前得到了这个标志，他们提到他们使用了`get-parameters`。
[![](img/844b13105522dffe1e7e7c21931e1af2.png)](https://i.giphy.com/media/l3q2K5jinAlChoCLS/giphy.gif) 
我运行`aws --version`，果然我用的不是最新版本，我用的版本还没有`get-parameters`命令和其他命令。回家后，我完全卸载了 awscli，更新了我的 repos，添加了 awscli 文档中建议的 repo，并重新安装了 awscli。我终于可以看到`get-parameters`的命令了。

```
antoinette@dev:~/secdsm/march$ aws ssm get-parameters --names secrets.zip
{
    "InvalidParameters": [], 
    "Parameters": [
        {
            "Version": 1, 
            "Type": "String", 
            "Name": "secrets.zip", 
            "Value": "Dodongo Dislikes Smoke"
        }
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

在阅读有关参数存储的内容时，我了解到参数存储将以键:值对的形式存储值。命令上的`--names`标志是‘secrets . zip ’,因为 python 脚本中的注释提到 pw 在文件名下。这意味着在键:值对中，文件名是键，pw 是值。

最后粘贴密码，解压文件，打印出 secrets.txt.
的内容

```
antoinette@dev:~/secdsm/march$ cat secrets.txt 
flag{iTsAsecReTtoEverYBodY} 
```

Enter fullscreen mode Exit fullscreen mode

这个故事的寓意:确保你的程序是最新版本，这样你就不会出错。

哦好吧。你赢了一些。你失去了一些。希望这对你有帮助！请在下面留下您的问题和评论。