# 谷歌代码堵塞到 I/O 的心理准备

> 原文：<https://dev.to/tiptopgs/google-code-jam-to-io-mental-preparation-4cn7>

这是我第一年尝试面向女性的 I/O 编程竞赛。谷歌举办年度代码堵塞挑战赛，这是他们第五年专门针对女性，以帮助增加在线竞赛空间的多样性和谷歌 I/O 开发者大会的代表性。最简单的部分是注册和决定我将在比赛中使用什么语言。作为参考，您可以查看解决方案和其他统计数据，包括过去几年[提交使用的最流行语言](https://www.go-hero.net/jam/17)。您可以使用任何编程语言(包括那些不在注册列表中的语言)。接下来是最困难的部分，那就是让自己在心理上为挑战做好准备，接下来是我为自己做准备的事情。

### 准备

为了准备，我正在复习过去几年的问题。我研究了一些解决方案，看看开发人员使用了什么库，以及他们是如何解析输入的。我想象这些应该是一样的，不管问题集是什么。我也在争论是用 python 还是 swift。我熟悉 swift，并经常在工作中使用它，但最近我一直在学习 python，这是使用的顶级编程语言之一，对于操作大型数据集非常强大。无论如何，我不想花太多时间做决定，所以我用[一些性能指标评测](https://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=swift&lang2=python3)，并且我尝试了一些样本挑战来熟悉这个过程，这样我就有可能更成功地参与竞争。

### 工具

我在 Xcode 中创建了一个新的命令行项目，并重用它来处理每个样本挑战集。命令行项目需要一个 main.swift 文件，所以我创建了单独的文件夹来包含问题解决方案和样本输入文件。我的文件夹结构看起来像这样:

[![](../Images/2a640f9b0b73cfc96ad1b49b20678d95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CAtNH21f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/526/1%2A-Kiynu8xLKZ7MtZwZw1R7Q.png)

注意，每个练习都有自己的 main.swift 和 ParseInput.swift 文件。我在实际的 Xcode 项目中只包含了一个 main.swift 文件，以解决我正在处理的问题，这样 Xcode 就不会混淆。对于每个挑战，解析输入文件的内容可能非常相似，因此所有可重用的读取方法都可以在 ParseInput.swift 文件中找到。作为提交过程的一部分，我选择通过标准输入来阅读示例文件的内容。因此，我创建了一个小的 shell 脚本来尽可能频繁和快速地编译/重新运行我的解决方案。

我的 shell 脚本如下所示: