# 节点模块问题

> 原文：<https://dev.to/leoat12/the-nodemodules-problem-29dc>

我想我不是第一个谈论这个问题的人，即使是在 dev.to。我快速研究试图找到任何解决方案，结论是这篇文章的标题图片。众所周知， **node_modules** 文件夹是存储项目依赖关系的地方。它的重量也是常识。

## 为什么我现在决定发泄我的挫败感

黑色星期五到了！这意味着折扣和更新电脑的机会。因此，我决定购买一个固态硬盘来提高我的笔记本电脑的性能，从 1 TB 硬盘到 500 GB 固态硬盘。我所有的文件总共有 299 GB，所以我不会损失太多的空间，但我还是决定做家务，包括备份我的项目。并不是我做的所有项目都放在 GitHub 上，有时我只是在尝试，不值得这么麻烦，但我还是保留了它们。

当我开始复制和粘贴过程时，我记得**节点模块**有多重...

## 有的比较

一个清楚显示问题的例子是我的 [ToRead CLI 项目](https://dev.to/leoat12/my-first-serious-project-in-nodejs-toread-cli-3e4b)的 **node_modules** 文件夹，如下图所示。

[![node_modules property window](img/9e96e1af61dee9b21b81c10551085e63.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3SWbgjQd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ncg9881ha4nndrtovfzu.PNG)

文件夹的大小并不是真正的问题，虽然我稍后会谈到，但 15.000 个文件和 1800 多个文件夹！？你在开玩笑吗？！这是一个简单的 CLI 项目，有 5 个文件！为了便于比较，我们来看看 **Windows** 文件夹中有多少个文件和文件夹:

[![Windows folder property window](img/eadb0ad024f76ba1c62cdaee43959b52.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dbUm7Ykt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9zbnztjbo2nbhwuflbf.PNG)

在系统计数的时候，我真的以为 **node_modules** 会赢，但是没有。在任何情况下，这个文件夹的文件量几乎是整个操作系统的一半！

正如我说过的，将 **node_modules** 文件夹从一个地方复制到另一个地方的问题不是大小，而是文件和文件夹的数量，以及树的复杂性。这是硬盘的噩梦。发现所有文件要花很多分钟，更不用说复制它们了。最后，它还会影响 **npm** 的表现，这也有模因。

[![Waiting for npm install](img/4fae8ebdb9f7f4cee4eef6784a5afbf5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yhX-rugr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/06505f9jenak444a48g8.jpg)

其他比较来自我对无服务器的热情。对我来说，用 Java 和 Javascript 实现同一个函数并不少见，因为您必须将函数与其依赖项捆绑在一起，所以比较哪一个在依赖项管理中更有效是一个好方法。在我的一个项目中，我注意到两种语言的函数具有几乎相同的特性，Java 包的大小是 11.1 MB，NodeJS 包的大小是 29.0 MB。因此，NodeJS 在依赖项的规模上也能做得更好。

## 其他语言做什么

除了 NodeJS 之外，我还有两种语言处理依赖关系的经验:Java 和 C#。在我看来，它们处理依赖关系的方式非常相似，而且比 NodeJS 更有效。

Java 有 Maven、Gradle 和其他依赖管理应用程序，它们的工作方式基本相同。有一个依赖关系的远程存储库，通常是 Maven Central 和一个本地存储库。Maven 总是首先在本地存储库中寻找依赖项，如果没有找到，就从远程存储库中下载。依赖项不在项目内部，像 **node_modules** 文件夹，它更全球化，它下载一次就可以被很多项目使用，只需添加到你的 pom.xml 中。

C#遵循同样的思路，你在一个. csproj 文件中列出你的依赖项，Nuget 处理依赖项，同时拥有一个远程和一个本地存储库。以这种方式处理依赖关系要高效得多，在本地任何项目中使用一次就下载一次。

我认为在文化和语言的构建方式以及人们眼中的图书馆方面也存在差异。Java 有一个非常成熟的核心库，可以处理几乎任何事情，不管是不是常见的场景。因此，Java 中的库通常是对 Java 已有内容的抽象，使其更易于使用。因此，这些库有一个更浅的依赖树，可以更快地到达 Java 核心库。

另一方面，我在 NodeJS 中看到的是相反的，一切都可以成为一个库，甚至是两个数相加的库(我希望是一个假设的例子),并且库严重依赖于另一个库，生成深度依赖树、许多文件和文件夹。

## 结论&讨论

我当然没有资格批评 NodeJs 的结构和工程，但是作为一个用户，我清楚地看到了一个问题和来自其他语言的一些教训，可以用来改进依赖性管理，这在今天对几乎每个应用程序都是至关重要的。你认为这个问题是怎么来的，已经做了什么来解决它？听听更有经验的开发人员是如何解决这个问题的，将会非常有趣。