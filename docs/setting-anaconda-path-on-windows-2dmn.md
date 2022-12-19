# 在 windows 上设置 Anaconda 路径

> 原文：<https://dev.to/hygull/setting-anaconda-path-on-windows-2dmn>

# 蟒蛇路径设置

我已经在`C:\Anaconda5.1Python3.6`目录下安装了 Anaconda。

在我的例子中，我有以下 5 个`paths`作为**路径**环境变量的值。我通过将`;`作为 2 个路径的分隔符，一个接一个地添加了这些路径。

```
C:\Anaconda5.1Python3.6
C:\Anaconda5.1Python3.6\Library\mingw-w64\bin
C:\Anaconda5.1Python3.6\Library\usr\bin
C:\Anaconda5.1Python3.6\Library\bin
C:\Anaconda5.1Python3.6\Scripts 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以启动新的 **Anaconda promt** 或 **Windows 命令行**并输入下面的命令来检查 Python 版本。

```
python --version 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过在 **Anaconda 提示符**或 **Windows 命令行**上键入下面的命令来运行 **Python 解释器**。

```
python 
```

Enter fullscreen mode Exit fullscreen mode

如果你能够进入 Python 解释器并执行 Python 语句，一切都没问题。