# 在 Python REPL / IPython / Jupyter 控制台中实时重载 Python 模块

> 原文：<https://dev.to/preslavrachev/live-reloading-of-python-modules-in-the-python-repl--ipython--jupyter-console-1d17>

通常，我会在一个 Python 模块上工作，并对其进行实时修改，然后我会想立即进行测试。小菜一碟！添加更改并再次执行脚本。嗯，这当然是一种方法，但是随着模块变大，他们倾向于导入其他模块，或者做一些初步的设置工作。

随着时间的推移，我已经养成了打开 Python REPL 的习惯(尽管我推荐使用 IPython 或 Jupyter 控制台)，导入我正在运行的模块并挑选出我想要测试的独立函数。问题是，当我对 Python 代码进行更改时(比如添加一个新函数)，它们不能立即使用，因为 Python REPL 和 IPython / Jupyter 都不会自动重新加载它们。

## 给我代码

是的，我们正谈到这一点

### 一般方式

Python 3 使用一个名为`reload`的函数支持模块的内联重载。它曾经是 Python 2 中的内置函数，但现在不再是这样了。如果您使用的是 Python 3.2+，您应该额外导入它:

对于 Python 3.2 和 3.3:

```
import importlib
importlib.reload(some_module) 
```

Enter fullscreen mode Exit fullscreen mode

对于 Python 3.4+:

```
import imp
imp.reload(some_module) 
```

Enter fullscreen mode Exit fullscreen mode

### IPython/Jupyter 魔法

Jupyter 附带了一组仅适用于 Jupyter/iPython 会话的扩展，称为 [magics](https://ipython.org/ipython-doc/3/interactive/magics.html) 。这些“魔法”之一是加载定制扩展的能力，[其中之一](http://ipython.readthedocs.io/en/stable/config/extensions/index.html)允许自动重新加载模块。为此，您应该在任何导入之前添加以下两个命令:

```
%load_ext autoreload
%autoreload 2
# you can check out the the documentation for the rest of the autoreaload modes
# by apending a question mark to %autoreload, like this:
# %autoreload? 
```

Enter fullscreen mode Exit fullscreen mode

这样，当您在代码编辑器中点击`Save`时，您应该能够再次重新运行 Jupyter 单元或 iPython 行，并且如果它正在调用您的模块，它应该会自动调用最新的版本。

## 链接

[Jupyter / IPython:编辑一个模块后，不重启内核修改无效——entthond 知识库](https://support.enthought.com/hc/en-us/articles/204469240-Jupyter-IPython-After-editing-a-module-changes-are-not-effective-without-kernel-restart)
[IPython 中的 python -重载子模块——堆栈溢出](https://stackoverflow.com/questions/5364050/reloading-submodules-in-ipython#5399339)

* * *

注意:这篇文章最初出现在[我的博客](https://preslav.me/2018/04/22/live-reloading-of-python-modules/)