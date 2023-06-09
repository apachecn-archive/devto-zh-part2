# 为多个版本的 python 创建 zip 文件

> 原文：<https://dev.to/tomoyukiaota/creating-a-pipfile-for-multiple-versions-of-python-9f2>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/87017a4f57b8246b894c)

由`pipenv install`命令创建的 Pipfile 具有 Python 的版本号。此 Pipfile 不能在装有不同版本 Python 的机器上使用。 <sup id="fnref1">[1](#fn1)</sup> 在这篇文章中，我将介绍如何创建一个可以用于多个版本的 Python 的 Pipfile。

# 什么问题？

当我们想在存储库中开始使用 Pipenv 时，我们要做的第一件事就是发出`pipenv install`命令。这将创建一个 Pipfile，如下所示:

```
[[source]]

url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[packages]

[dev-packages]

[requires]

python_version = "3.6" 
```

Enter fullscreen mode Exit fullscreen mode

这个 Pipfile 是在装有 Python 3.6.4 的 Windows PC 上生成的。

然后，如果我们将这个 Pipfile 复制到另一台使用 Python 3.7 的 PC 上并发出`pipenv install`，我们将会遇到下面的错误消息:

```
Requested Python version (3.6) not installed
Warning: Python 3.6 was not found on your system…
You can specify specific versions of Python with:
  $ pipenv --python path\to\python 
```

Enter fullscreen mode Exit fullscreen mode

在装有 pyenv 的 macOS/Linux 上，[Pipenv 可以使用 pyenv](https://pipenv.readthedocs.io/en/latest/advanced/#automatic-python-installation) 安装所需的 Python 版本，但 pyenv 在 Windows 上不可用。

# 解

如果不需要指定 Python 版本，我们可以删除`[requires]`部分。然后，不管机器上安装的 Python 版本如何，`pipenv install`命令都会成功。

```
 [[source]]

url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[packages]

[dev-packages] 
```

Enter fullscreen mode Exit fullscreen mode

我在这里所做的在官方文件中有所描述。
[https://pipenv . readthedocs . io/en/latest/basics/# specifying-versions-of-python](https://pipenv.readthedocs.io/en/latest/basics/#specifying-versions-of-python)

> 包含[requires] python_version = "3.6 "指定您的应用程序需要此版本的 python，并且将在将来针对此 Pipfile 运行 pipenv install 时自动使用(例如，在其他机器上)。如果不是这样，请随意删除这一部分。

采用此方法时，Pipfile.lock 不应包含在版本控制中。这是因为 Pipfile.lock 的内容可能会因 Python 版本的不同而有所不同。
[https://pipenv . readthedocs . io/en/latest/basics/# general-recommendations-version-control](https://pipenv.readthedocs.io/en/latest/basics/#general-recommendations-version-control)

> *   If the target is multiple versions of Python, don't keep Pipfile.lock in version control.

* * *

1.  确切地说，当主版本或次版本不同时，Pipfile 不能使用。例如，Python 3.6 的 Pipfile 不能在装有 Python 3.7 的机器上使用。另一方面，当只有补丁版本不同时，可以使用 Pipfile。例如，Python 3.6.4 的 Pipfile 可以在装有 Python 3.6.5 的机器上使用。 [↩](#fnref1)