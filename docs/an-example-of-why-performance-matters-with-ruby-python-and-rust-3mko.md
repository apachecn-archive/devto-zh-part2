# 为什么性能很重要的一个例子(Python 和 Rust)

> 原文：<https://dev.to/rhymes/an-example-of-why-performance-matters-with-ruby-python-and-rust-3mko>

## 长篇介绍

2017 年 9 月，我在 dev.to 上的第一篇真实帖子如下:

[![rhymes](img/91ed8188209dceb0374b6a8c88308333.png)](/rhymes) [## TrailDB 上百万行的冒险，Python 和 Go

### 押韵 Sep 22 ' 17 12min read

#traildb #python #go](/rhymes/adventures-in-traildb-with-millions-of-rows-python-and-go)

我试图从大约 60 GB 的 CSV 文件中提取与 1.39 亿个事件相对应的信息。我从 Python 开始，看看它是如何工作的。这个实验是由我对红移的挫败感引发的，因为我想玩一玩 [TrailDB](http://traildb.io/) ，一个查询事件系列的库。我的测试是不科学的，但是在切换到 Go(通过复制和粘贴代码，因为我当时并不真正了解这种语言)后，我能够以比 Python 快 2.6 倍的速度设置数据库，并以快 2.54 倍的速度查询数据。

速度和性能的话题对于这个网站上的每个人来说都是很重要的，即使速度和性能可以与上下文相关(参见“足够快”的概念)。你可以看到这个话题渗透到 dev.to 上关于网络速度慢、浏览器和桌面应用的内存占用以及其他相关话题的对话中。附上几个有趣的长时间讨论的好例子:

[![quii](img/57e087adeae522901488437fbb68db84.png)](/quii) [## 我想要的网络

### 克里斯詹姆斯 Aug 20 ' 18 分钟阅读

#web #html #performance #a11y](/quii/the-web-i-want-43o)

由 [@quii](https://dev.to/quii)

[![tux0r](img/9c55a9608e1aa4a13ec42e87423999a7.png)](/tux0r) [## 你的网站真的需要比 Windows 95 大吗？

### tux0r Sep 23 ' 181 分钟读取

#discuss #coding #technology](/tux0r/does-your-website-really-need-to-be-larger-than-windows-95-16mm)

by [@tux0r](https://dev.to/tux0r)

## 你为什么在这里看这个

没有人会反对提高速度的成本效益，这就引出了这篇文章的主旨。andréArko(Ruby 的 [Bundler](https://bundler.io/) 的首席开发者)写的一篇题为[用 Rust](https://andre.arko.net/2018/10/25/parsing-logs-230x-faster-with-rust/) 解析日志快了 230 倍的文章引起了我的注意。

从那以后我就知道了铁锈的速度...这一点以及它在内存管理方面的优势是每个人在谈论 Rust 时都会谈到的:-D

从那以后，我切换到两个我每天在命令行使用的基于 Rust 的工具: [bat](https://github.com/sharkdp/bat) 而不是`cat`，尤其是 [ripgrep](https://github.com/BurntSushi/ripgrep) 而不是`grep`和`ack`。速度提升肉眼可见(感谢 [@dmfay](https://dev.to/dmfay) 的提示)！

回到文章。Arko 想要查询 Bundler 每天 500 GB 日志的宝库，以提取关于社区的有用信息。每个日志文件都包含 JSON 中的数百万个事件(顺便说一句:如果可以的话，使用结构化日志，JSON 或 key-value，以后你会感谢我的)。目前，这些文件被压缩在 S3 的一个桶里，每月收费几美元。

托管日志解决方案太贵了，所以他试着看看是否能做点什么。

第一次尝试是在 Ruby 中进行的，一天的数据花费了疯狂的 16 个小时。没有。

第二次尝试是在 Python 中使用 AWS Glue 和 Amazon 服务器的全部功能。他使用 100 名并行工作人员，每月花费 1000 美元，每个日志文件(总共 500 个)只需 3 小时，平均 36 分钟。没有。

第三次尝试是在 Rust。他最初减少到每个文件 3 分钟，然后减少到每个文件 60 秒。在对它进行了更多的修改并收到了读者的反馈后，他成功地在 8 秒钟内解析了一个文件。！).

第四次尝试又是在 Rust 中，他使用了并行化。它比顺序尝试快 3.3 倍。他就是这样得出题目中的 230 倍乘法因子的。

### 关于比较的几点说明

如果你仔细阅读，你会注意到以下几点:

*   第一次尝试可能不应该在帖子中提到，因为它收集的数据比其他的少(我们不知道少了多少)
*   如果按顺序运行，Rust 的第一次尝试需要 8.33 小时，比 Python 和 Glue 的实验快 30 多倍
*   Rust 中的最后一个“顺序”实验对于整个 500 GB 的数据集来说只需要 1 个多小时，这是一个巨大的加速

### 部署时间

André Arko 谈到的最后一件事是他如何设法部署 Rust 脚本，以便它可以在 AWS 上存储的生产日志上工作。这部分让我发笑:

> 我发现了 rust-aws-lambda，它可以让你的 rust 程序通过伪装成 Go 二进制程序在 AWS Lambda 上运行

将应用程序作为二进制:D 分发的另一个奇迹

在 AWS Lambda 上，他获得的加速是最初 Python 例子的 78 倍，不错！

他做了一些计算，它安全地位于 AWS Lambda 的自由层。

于是他从一个月 1000$到一个月 0，靠的就是用 Rust 重写一个剧本。

我检查了脚本的[库，人们已经提出了让它更快的方法😂](https://github.com/rubytogether/kirby)

## 如果你已经走了这么远，需要考虑的事情

*   性能可以为你节省很多钱
*   知道(或愿意学习)多种语言是个好主意
*   对于这种解析，Rust 绝对值得一看
*   有时候更好比足够好更好