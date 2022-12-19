# 终端中的备忘单

> 原文：<https://dev.to/koenighotze/cheat-sheets-in-your-terminal--1dli>

在听了第 283 集[的播客](https://changelog.com/podcast/283)之后，我决定在命令行上使用 [Devhints](https://devhints.io) 的超棒的小抄库。

这是我想出来的:如果你想阅读 React 的备忘单，下载下面的要点，保存为`hint`并键入:

```
$ hint react 
```

Enter fullscreen mode Exit fullscreen mode

瞧，床单出现了。

[![This is what the tool looks in your terminal](img/7e649b0c85e9a59f08ef3a474f33f1ae.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p-3uoVD4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7r0s35l3vdwaai1wcgwk.gif)

shell 脚本需要`wget`和`mdless`，您可以使用您喜欢的包管理器来安装它们。这些表是从 Devhints 中获取的。木卫一的 [Github 库](https://github.com/hazeorid/devhints.io)。

工作表文件缓存在`$HOME/.hack`中，可以使用`--refresh`命令行参数刷新。

如果你喜欢终端中的备忘单，你可能还想看看 TLDR 的文章，它用例子解释了工具——基本上它只是颠倒了经典的手册页；)

无论如何...

如果您有更好的方法或想法来改进这个小片段，请告诉我: