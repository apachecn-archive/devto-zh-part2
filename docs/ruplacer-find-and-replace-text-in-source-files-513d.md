# ruplacer:在源文件中查找和替换文本

> 原文：<https://dev.to/dmerejkowsky/ruplacer-find-and-replace-text-in-source-files-513d>

# 简介

今天我想谈谈我一直在开发的一个命令行工具。

它叫做 [ruplacer](https://github.com/SuperTanker/ruplacer) ，顾名思义，它是 *rually* 酷，用铁锈写的。

基本上，它在源文件中查找并替换文本。下面是 ruplacer 的一个截图:

[![ruplacer screenshot](img/f4edb4979ccc5bc43ea5df9523de5003.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VIY745ja--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dmerej.info/blog/pics/ruplacer.png)

一些不错的功能:

*   跳过`.gitignore`中列出的文件，以及二进制文件
*   默认情况下以模拟运行模式运行(使用`--go`将更改实际写入文件系统)
*   默认情况下搜索当前工作目录，尽管在模式和替换之后可能会指定其他路径。
*   使用 Rust 正则表达式，这意味着您可以捕获模式中的组，并在替换中使用它们。例如:

```
# Replaces dates looking like DD/MM/YYYY to YYYY-MM-DD
$ ruplacer '(\d{2})/(\d{2})/(\d{4})' '$3-$1-$2' 
```

Enter fullscreen mode Exit fullscreen mode

*   如果模式只是一个子串，不应该作为正则表达式使用，那么可以用`--no-regex`运行
*   最后但同样重要的是，还有一个`--subvert`模式，允许您更换各种款式的表壳:

```
$ ruplacer --subvert foo_bar spam_eggs
Patching src/foo.txt
-- foo_bar, FooBar, and FOO_BAR!
++ spam_eggs, SpamEggs, and SPAM_EGGS! 
```

Enter fullscreen mode Exit fullscreen mode

# 工作原理

它是这样工作的:

首先，我们为命令行参数解析构建一个 [structopt](https://crates.io/crates/structopt) struct。根据`--subvert`或`--no-rexeg`标志的存在，我们构建一个*查询*，它可以是几种类型:`Substring`、`Regex`或`Subvert`。

然后我们利用[忽略](https://crates.io/crates/ignore)箱遍历源目录中的每个文件，同时跳过`.gitignore`中列出的文件。顺便说一下，忽略板条箱直接来自 [ripgrep](https://github.com/BurntSushi/ripgrep) ，它是`grep`的一个很棒的替代品，也是用 Rust 写的。

在这个过程中，我们从源文件和查询构建了一个*文件修补器*。文件修补程序检查文件的每一行，然后将它和查询一起发送给*行修补程序*。

LinePatcher 运行对应于查询类型的代码并返回一个新字符串，如果需要，使用[变形器](https://crates.io/crates/Inflector)来执行大小写字符串转换。

最后，如果字符串已经更改，文件修补程序将构建一个*替换*结构，并将其漂亮地打印给用户。在这样做的同时，它还保留了文件修改内容的记录。最后，如果不是在模拟运行模式下，它会用新内容覆盖文件。

差不多就是这样:)

# 为什么我要分享这个

ruplacer 的想法始于大约十年前，当时我的一个同事向我展示了一个名为`replacer`的 shell 函数(谢谢，Cédric！)基本上是对`find`、`sed`和`diff`的混合调用。你还可以在网上找到。

因为我想要更好的跨平台支持、模拟运行模式和丰富多彩的输出，几年前我用 Python 重写了它。在这个过程中，特性、命令行语法和输出的风格都发生了很大的变化，但我一直在定期使用它。

最后，在听说了 ripgrep 和 [fd](https://github.com/sharkdp/fd) 之后，我决定试一试 Rust，这个工具的第三个化身 ruplacer 就是这样诞生的。这让我相信它足够好，值得*你*去尝试。

如果你已经安装了`cargo`，运行`cargo install ruplacer`就可以得到 ruplacer。否则你会在 GitHub 上找到[源代码](https://github.com/SuperTanker/ruplacer/tree/master/src)和[预编译二进制](https://github.com/SuperTanker/ruplacer/releases)。

干杯！

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。