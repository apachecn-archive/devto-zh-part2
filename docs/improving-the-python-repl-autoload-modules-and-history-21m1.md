# 改进 python REPL(自动加载模块和历史)

> 原文：<https://dev.to/elabftw/improving-the-python-repl-autoload-modules-and-history-21m1>

亲爱的读者你好，

在编写 python 代码时，只需在终端中键入`python`来测试一些表达式，就可以启动 REPL (Read-Eval-Print-Loop)。

[![python](img/7dbefcee4d7cdf88bbd6f2472f4216e3.png)T2】](https://xkcd.com/353/)

但问题是，它非常简单。无历史记录，默认情况下不加载任何模块。我不知道你怎么想，但是 99%的时间我都需要最少使用`numpy`和`pandas`。大概`matplotlib`也是。

**谁有时间每次都打`import xyz`？不是我。**

下面是如何在 python 的 REPL 中自动加载模块:

## 从创建一个~/开始。pystartup 文件