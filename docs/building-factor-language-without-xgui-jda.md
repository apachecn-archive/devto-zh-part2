# 没有 X/GUI 的构建因子语言

> 原文：<https://dev.to/cess11/building-factor-language-without-xgui-jda>

在过去的几周里，我在栈语言上比平时拖得更久，并且涉猎了一点叫做 Factor 的实用语言。它有很多电池，但没有绒毛，一些边缘非常锋利，你知道吗，话说回来，它非常强大，至少在保罗·格拉厄姆的这篇著名文章和其他文章中是这样说的。

当试图在一个无头的 Debian 服务器上运行 Factor 语言的预构建二进制文件时，遇到了这个问题。基本上它期望找到一个共享的 GTK 库，那里没有任何库，因为在这种类型的环境中既没有安装 X 也没有安装 GTK。作为一种变通方法，可以在自己的路径中放置一个同名文件，但即使对我来说，这似乎也有点太难看了。

遵循对
的建议

```
git clone git://factorcode.org/git/factor.git 
```

Enter fullscreen mode Exit fullscreen mode

然后我在'中添加了' NO_UI=1 '。/build.sh '，基本上就在 hashbang 之后。我没有对脚本做任何修改就运行了

```
./build.sh update 
```

Enter fullscreen mode Exit fullscreen mode

第一次尝试时，它可以像 CLI 界面一样编译和运行。

在对这个类似 Forth 的工具做了一些实验后，我印象很深，如果你厌倦了 Python 的缓慢和特别的品质，那就来看看黑暗和面向堆栈的一面，在那里编程是串联的、奇怪的和强大的。标准库和 FFI 相当不错，似乎足以在从系统到应用程序和 web 的几乎所有公共领域进行探索性和动态编程。

[黑客快乐！](http://factorcode.org/)

Stagling@Patreon