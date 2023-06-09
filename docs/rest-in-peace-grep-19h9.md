# 安息吧，格雷普！

> 原文：<https://dev.to/abhinav/rest-in-peace-grep-19h9>

我不是说`grep`很慢或者有任何缺陷，但是它肯定可以更快(更好)。GNU grep 并不是唯一的玩家。

我来介绍一下 [`ripgrep`](https://github.com/BurntSushi/ripgrep) ，Rust 写的 grep/ag/ack 替代。

那么为什么要用`ripgrep`？因为它很快。非常快！它有更理智的默认。而且是用铁锈写的。(下一次的话题:)

> 另外，我刚刚了解到 ripgrep 支持 Visual Studio 代码的搜索。

## 好处

*   我告诉过你这太快了吗？
*   默认情况下，它会递归搜索。
*   默认情况下，它会忽略隐藏文件和二进制文件。
*   它尊重. gitignore。默认情况下，它会跳过列出的文件和目录。
*   您可以将搜索限制在特定的文件类型。
*   它打印起来很漂亮。
*   它支持除 UTF 8 以外的文件编码。
*   太快了。

## 明白了

*   没有多行搜索。
*   由于大量使用线程来完成工作，ripgrep 的输出是不确定的。提示:通过`sort`传输输出。

> 虽然它不是 GNU grep 或 ag 的替代品。所以在没有测试的情况下，不要在脚本中用 rg 代替它们。

## 安装

如果您安装了 Rust toolchain (1.20 或更新版本)，您可以使用 cargo 安装它。如果还没有添加到$PATH 中，请添加`~/.cargo/bin`。

```
$ cargo install ripgrep 
```

Enter fullscreen mode Exit fullscreen mode

如果你跑出血边拱，跑

```
$ pacman -S ripgrep 
```

Enter fullscreen mode Exit fullscreen mode

Fedora 用户可以使用
来安装它

```
$ dnf install ripgrep 
```

Enter fullscreen mode Exit fullscreen mode

在 macOS 上，运行

```
$ brew install ripgrep 
```

Enter fullscreen mode Exit fullscreen mode

如果你担心每次搜索都要多输入 3 个字母，并且不知道别名是什么，不要担心。这个二进制文件就叫做`rg`。(:

## 用法

如果你知道如何使用`grep`，你可以使用`ripgrep`。不过，我在这里只概述一下基本用法。如果你想了解它的一切，请阅读它的 GitHub 页面上的说明。

在目录中递归搜索任何单词:

```
$ rg <keyword> 
```

Enter fullscreen mode Exit fullscreen mode

> `ripgrep`的默认行为是跳过隐藏和二进制文件，除了 git 忽略的所有内容。使用`-uuu`将其禁用。

要仅在特定文件类型中搜索关键字，请将文件扩展名传递给-t 开关:

```
$ rg -tjs foo 
```

Enter fullscreen mode Exit fullscreen mode

在匹配指定 glob:
的文件中搜索关键字

```
$ rg foo -g 'bar.*' 
```

Enter fullscreen mode Exit fullscreen mode

## 基本基准

我在我的机器(Core i7 6500U，8GB RAM，基于 Ubuntu 16.04 的 KDE neon 5.12.5)上使用`/usr/bin/time` binary 运行了简单的基准测试，ripgrep 似乎每次都能击败 GNU grep(以巨大的优势！).

```
$ /usr/bin/time rg -uu import > /dev/null # ~24 seconds

$ /usr/bin/time grep -r import * > /dev/null # ~3 min 27 seconds 
```

Enter fullscreen mode Exit fullscreen mode

请记住，这些无论如何都不是科学基准。前往 ripgrep 的 [GitHub 页面](https://github.com/BurntSushi/ripgrep)获取更全面的数字。

* * *

本帖原载于我的[网站](https://abhnv.com/blog/2018/05/16/rip-grep.html)。