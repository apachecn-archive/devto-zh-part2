# 为 Jupyter 笔记本电脑设置新的 Python 虚拟环境

> 原文：<https://dev.to/seanlane/setting-up-a-new-python-virtual-environment-for-jupyter-notebooks-4aca>

我的许多实验室工作和课程工作都涉及到 Jupyter 笔记本的使用，尽管 Python 的依赖性需要与其他领域发生冲突。我一直在使用 [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) 将这些和其他项目环境相互隔离。这篇文章介绍了为 Jupyter 笔记本安装一切所需的东西，并在一个干净的 Python 环境中运行，每个环境都有单独的内核，包括安装添加了社区开发功能的`jupyter_contrib_nbextensions`。

## 初始设置

这只需要在您的机器/用户帐户上完成一次，以便为 Python 创建无限量的虚拟环境准备好构建块。首先，您应该在您的机器上安装一个合适的 Python 副本。对于 macOS，我推荐使用[家酿](https://brew.sh)包管理器(安装说明见链接)，然后安装 Python。请注意，我使用的是 Python 3，因为 Python 2 将在 2020 年寿终正寝，但如果你在 macOS 上，也可以考虑通过自制软件安装 Python 2，因为系统版本似乎已经过时了。不管怎样，通过自制软件在 mac 上安装:

```
$  brew install python3 # Follow any instructions given here from the output 
```

在基于 Ubuntu/Debian 的系统中:

```
$  sudo apt-get install python3 python3-pip 
```

在基于 Arch Linux 的系统上:

```
$  pacman -S python-virtualenvwrapper 
```

现在，假设 Python 3 和`pip`都已安装，安装`virtualenvwrapper`并根据这些说明修改您的 shell 启动文件:[安装`virtualenvwrapper`](https://virtualenvwrapper.readthedocs.io/en/latest/install.html) 。我为我的系统做了以下工作:

```
$  sudo pip install virtualenvwrapper
$  echo "export WORKON_HOME=$HOME/.virtualenvs" >> $HOME/.profile
$  echo "source /usr/local/bin/virtualenvwrapper.sh" >> " >> $HOME/.profile $  source ~/.profile 
```

## 创建新的虚拟环境

现在，每当您需要创建一个新环境时，请使用下面的示例。我的示例 virtualenv 将被命名为`example`，我们将安装 Jupyter 和任何其他依赖项，我们将向`$VIRTUAL_ENV/bin/postactivate`添加一行，以便在激活环境时，我们当前的工作目录将被切换到我们的项目目录`~/path/to/example/code`。

```
$  mkvirtualenv example -p python3 # Note we specify which interpreter to use
(example) $  echo "cd $HOME/path/to/example/code" >> $VIRTUAL_ENV/bin/postactivate
(example) $  pip install ipykernel
(example) $  pip install jupyter_contrib_nbextensions
(example) $  jupyter contrib nbextension install --sys-prefix # Kinda important
(example) $  pip install jupyter_nbextensions_configurator
(example) $  jupyter nbextensions_configurator enable --sys-prefix
(example) $  python -m ipykernel install --user --name=example
(example) $  pip install <anything-else-you-want> 
```

注意，在创建 virtualenv `example`之后，环境被自动激活(这可以通过终端中的`(example)`前缀以及运行`which python`来判断，后者应该输出一个到属于该环境的 Python 解释器的路径)。当激活时，任何对`python`的调用都使用环境的 Python 解释器和`pip`，这就是为什么我们不需要调用`pip3`而不是`pip`。注意，为了安装`jupyter contrib nbextension`和`jupyter nbextensions_configurator`，我们使用了选项`--sys-prefix`，它配置这些扩展用于虚拟环境，而不是全局系统环境，这是我们试图隔离的。