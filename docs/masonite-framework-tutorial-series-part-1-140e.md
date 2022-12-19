# Masonite 框架教程系列第 1 部分-安装

> 原文：<https://dev.to/masonite/masonite-framework-tutorial-series-part-1-140e>

[![](../Images/a5749b28405e60871556cfd1d9e2fb84.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mAa7WguO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0qq93lukfnwntt1e5iyq.png)

# 简介

我是乔·曼库索， [Masonite](http://github.com/MasoniteFramework/Masonite) 的创作者，我将带来其中的一些教程文章，我最终会用它们来制作一个视频系列。

如果你想了解 Masonite 的系列文章和所有未来的文章，请务必关注！我会每隔几天发布一个新的教程，所以请保持关注！

本系列将更深入地研究每个概念，我们将给出一个实际的例子，并对每个部分进行更深入的观察。这些教程将类似于官方文档，但我们将有空间去更深入这里比我们可以在文档中。

我们将不会建立一些具体的东西，但在每个部分开始“新鲜”。您可以使用这些概念性知识来构建令人敬畏的应用程序。

本文将讨论如何安装 Masonite。

# 什么是泥瓦匠

Masonite 是一个现代的以开发者为中心的 Python web 框架。Masonite 采用了与许多 Python 框架完全不同的方法来构建 Python 应用程序。Masonite 的架构更类似于 Laravel 框架，并借用了许多文件结构和设计概念，如 IOC 容器、控制器方法的自动解析依赖注入、助手函数、管理器模式和 Laravel 使用的许多设计模式。

Masonite 是一个完全包含电池的 Python 框架，有太多的功能，不胜枚举。要获得更详尽的特性列表，你可以点击这里查看官方文档。或者，您可以跟随本系列教程，最终找到每个特性的详细解释。

# 安装

Masonite 附带了一个名为 Craft 的 CLI 工具。Craft 命令会成为你最好的朋友，你会一直使用它们。Craft 命令类似于 Laravel 的 Artisan 命令或 Ruby on Rails 的 Rails 命令行；如果你来自那个世界。

## 安装工艺

我们可以简单地通过安装 Masonite CLI 工具来安装 craft:

```
$ pip install masonite-cli 
```

Craft 可以在 Python 2.7+和 Python 3.4+上运行，因此它不需要从 pip3 安装(pip 3 将在 Python 3 安装下安装)。

* * *

<center>
* * * *
</center>

如果您得到一个类似 SSL 证书错误的错误，那么您可能有一个过时的 OpenSSL 或 LibreSSL 版本(如 OpenSSL 0.9)。您的机器很可能已经有这个需求，但是您需要安装 OpenSSL 1.0+或 LibreSSL 2.2.7+。我们可以通过运行以下命令来检查这一点:

```
$ openssl version 
```

如果你的 OpenSSL 版本低于上面推荐的版本，那么你需要升级它们。

<center>
* * * *
</center>

* * *

一旦安装完毕，我们就可以使用几个手工命令了。我们可以通过运行以下命令来检查这些命令:

```
$ craft 
```

应该会看到如下输出:

```
Craft Version: 2.0.10

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help     Displays help for a command
  install  Installs all of Masonite's dependencies
  list     Lists commands
  new      Creates a new Masonite project
  package  Creates a new package 
```

您将看到我们有一个名为`new`的命令，因此让我们运行该命令来启动并运行我们的项目:

```
$ craft new project_name 
```

这个命令要做的是下载位于`MasoniteFramework/masonite` GitHub 存储库的存储库的 zip 文件，解压缩该文件，并将刚刚下载的目录重命名为您的项目名称。

我们现在可以`cd`进入我们的项目:

```
$ cd project_name 
```

## 激活虚拟环境(可选)

您不必执行这一步，但是为您正在构建的每个应用程序激活虚拟环境是有好处的，这样您就不会混淆和组合应用程序的不同依赖项。无论您是在构建 Masonite、Django 还是 Flask 应用程序，为每一个应用程序提供隔离的虚拟环境都是有好处的。

Python3 自带一个名为`venv`的内置虚拟环境模块。在 Mac 和 Windows 上，我们可以通过运行以下命令来创建一个:

```
$ python3 -m venv venv 
```

最后一个参数可以是您不希望虚拟环境文件夹被调用的任何名称。我总是叫我的`venv`。现在我们只需要激活我们的虚拟环境:

在 Mac 上，我们可以运行:

```
$ source venv/bin/activate 
```

在 Windows 上，我们可以运行:

```
$ ./venv/Scripts/activate 
```

我们现在应该在一个虚拟环境中，我们将知道我们是否成功，因为我们现在应该在我们的终端命令行旁边看到我们的虚拟环境名称:

```
(venv) My-MBP:project_name username$ 
```

## 安装依赖项

通常我们现在会运行类似于`pip install -r requirements.txt`的东西，但是如果我们这样做的话，我们需要采取一个额外的步骤，那就是为我们的应用程序生成一个密钥。Craft 结合了这两个步骤，我们可以只运行一个命令:

```
$ craft install 
```

这个命令将为我们做三件事。它要做的第一件事是复制我们创建 Masonite 应用程序时创建的`.env-example`文件中的所有数据，它将运行 2 个命令:

```
$ pip install -r requirements.txt
$ craft key --store 
```

这样做的目的是安装所有的需求，然后为我们的应用程序生成一个密钥，并把它放到我们的`.env`文件中。`craft key`命令来自我们的应用程序本身。

每个 Masonite 应用程序都可以有自己的命令集。如果 craft 命令在 Masonite 应用程序中运行，那么除了它自己的命令之外，它还会获取那些应用程序的所有命令。

# 上菜泥瓦匠

我们现在可以运行我们的 Masonite 应用程序了！我们可以简单地通过运行`serve`命令来实现:

```
$ craft serve -r 
```

注意这个`-r`标志。这样做的目的是用 waste 运行 WSGI 服务器，并将服务器设置为自动重载运行。每当您对应用程序进行更改并保存文件时，它都会刷新 WSGI 服务器，因此您不必在每次进行更改时都停止并重新运行服务器。

该命令将在`http://localhost:8000`运行服务器。我们可以通过两个选项来改变这一点:

```
$ craft serve -r --host 127.0.0.1 --port 8080 
```

* * *

这就是第 1 部分！您现在拥有了一个功能齐全的 Masonite 应用程序！如果你不想等待这个系列的下一部分，那么你可以在这里查看官方文档。

请务必跟进，以免错过第 2 部分！