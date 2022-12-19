# rusync 简介

> 原文：<https://dev.to/dmerejkowsky/introducing-rusync-275>

*最初发表于[我的博客](https://dmerej.info/blog/post/introducing-rusync/)。*

今天我写了我的第一个“真正的”rust 项目。

它是《Rust》中`rsync`的重写版，名为`rusync`。

下面是它的安装和使用方式:

```
$ cargo install rusync
$ rusync test/src test/dest
:: Syncing from test/src to test/dest …
-> foo/baz.txt
-> foo/bar.txt
 ✓ Synced 2 files (1 up to date) 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 github 上找到来源[。](https://github.com/dmerejkowsky/rusync)

# 反馈请求

我写这个是因为我想试一试 Rust。

如果你已经是一个 Rust 开发者，如果你能对我写的代码给我一个诚实的评价，我将不胜感激。

查看[反馈页面](https://dmerej.info/blog/pages/feedback/)了解所有可能联系到我的方式，在此预先表示感谢！

# 接下来是什么

以下是我计划实现的功能列表:

*   删除无关文件的选项
*   全局进度条

最后一点很有趣:我们需要递归地遍历源文件夹中的所有文件，以便估计传输的总大小，但是我们希望在传输过程中执行这个操作*。*

这将是一个尝试 Rust 并发特性的机会:)

干杯！