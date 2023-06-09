# 创建一个 Pipfile，它根据操作系统有不同的安装说明(例如 PyTorch v0.4.1)

> 原文：<https://dev.to/tomoyukiaota/creating-a-pipfile-which-has-different-installation-instructions-depending-on-operating-systems-pytorch-v041-as-an-example-56i8>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/5fe5b1d1a4e9fc475cf4)

在本文中，我将描述当我们根据操作系统有不同的安装说明时，如何创建 Pipfile。

作为一个实际的例子，我将为 [PyTorch](https://pytorch.org/) v0.4.1 创建一个可以在 macOS/Windows 上使用的 Pipfile。

# 简介

对于大多数 Python 包，安装说明是相同的，与操作系统无关。比如`numpy`可以这样安装:

```
pipenv install numpy 
```

Enter fullscreen mode Exit fullscreen mode

但是，根据操作系统的不同，有些软件包有不同的安装说明。例如， [PyTorch](https://pytorch.org/) v0.4.1 在 macOS 上通过以下命令安装:

```
pipenv install torch 
```

Enter fullscreen mode Exit fullscreen mode

在 Windows 上，它是这样安装的:

```
pipenv install http://download.pytorch.org/whl/cpu/torch-0.4.1-cp36-cp36m-win_amd64.whl 
```

Enter fullscreen mode Exit fullscreen mode

(在这个例子中，我假设 PyTorch 的安装是针对没有 CUDA 的 Python 3.6 的。)

在 Pipfile 中，我们需要让前一条指令运行在 macOS 上，让后一条指令运行在 Windows 上。如何才能实现？

# 解

根据操作系统指定`sys_platform`。[在 Pipenv](https://docs.pipenv.org/advanced/#specifying-basically-anything) 的官方文档中，描述了只在 Windows 上安装`pywinusb`的情况。

对于 PyTorch v0.4.1，我实际使用的 Pipfile 是这样的:

```
[[source]]

url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]

theano = "*"
tensorflow = "*"
keras = "*"
numpy = "*"
scipy = "*"
matplotlib = "*"
seaborn = "*"
jupyter = "*"
jupyterlab = "*"
ipython = "*"
pandas = "*"
scikit-learn = "*"
spyder = "*"
pillow = "*"
torch = {version = "==0.4.1", sys_platform = "== 'darwin'"}
"b4b7455" = {file = "http://download.pytorch.org/whl/cpu/torch-0.4.1-cp36-cp36m-win_amd64.whl", sys_platform = "== 'win32'"}
torchvision = "*"

[dev-packages]

pylint = "*"

[requires]

python_version = "3.6" 
```

Enter fullscreen mode Exit fullscreen mode

在`[packages]`中，PyTorch v0.4.1 有两行。

```
torch = {version = "==0.4.1", sys_platform = "== 'darwin'"}
"b4b7455" = {file = "http://download.pytorch.org/whl/cpu/torch-0.4.1-cp36-cp36m-win_amd64.whl", sys_platform = "== 'win32'"} 
```

Enter fullscreen mode Exit fullscreen mode

在这些行中，`sys_platform = "== 'darwin'"`指定这仅适用于 macOS，`sys_platform = "== 'win32'"`指定这仅适用于 Windows。

对了，在 Windows 上执行`pipenv install http://download.pytorch.org/whl/cpu/torch-0.4.1-cp36-cp36m-win_amd64.whl`的时候，`"b4b7455"`是写在 Pipfile 上的。然后我加上了`sys_platform = "== 'win32'"`。

在 GitHub 中，我创建了一个存储库，[pipenv _ cross-platform _ example](https://github.com/TomoyukiAota/pipenv_cross-platform_example)，把 Pipfile 和 Pipfile.lock 放在那里，所以这个存储库作为例子可能会有用。