# 我对我的 Python 项目中` __init__.py '文件中的这段代码有点困惑。这在内部是如何工作的？

> 原文：<https://dev.to/seanballais/i-got-surprised-by-this-code-in-the-init-file-in-my-python-package-how-does-this-work-internally-4ob0>

因此，下面的 Python 代码驻留在我的 Python 项目的`__init__.py`文件中，这是我使用 PyScaffold 自动生成的。

```
from pkg_resources import get_distribution, DistributionNotFound

try:
    # Change here if project is renamed and does not equal the package name
    dist_name = 'mlfq-sim'
    __version__ = get_distribution(dist_name).version
except DistributionNotFound:  # pragma: no cover
    # Can't unit test this for now since I do not know how to temporarily
    # remove a distribution during runtime. Help. Also, is this even
    # worth it?
    __version__ = 'unknown' 
```

Enter fullscreen mode Exit fullscreen mode

我知道它得到了我的项目的版本。但是它是怎么做到的呢？与下面的相比，使用它有好处吗？

```
__version__ = 'some version' 
```

Enter fullscreen mode Exit fullscreen mode