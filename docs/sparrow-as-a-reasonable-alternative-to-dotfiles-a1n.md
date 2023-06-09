# Sparrow 作为点文件的合理替代品

> 原文：<https://dev.to/melezhik/sparrow-as-a-reasonable-alternative-to-dotfiles-a1n>

几乎每个月或更频繁地，我都能在 dev.to 和其他网站上看到关于人们如何使用 git 和 bash 管理 bashrc/dot 文件的帖子。

使用这些工具来管理您的服务器配置是可以的，但是让我展示您更灵活和更强大的方法。

Sparrow 是一个有效管理和移植服务器配置的工具。

该方法包括以下步骤:

*   将配置片段定义为 sparrow 任务

*   将任务保存为本地目录中的普通旧文件

*   将文件推送到远程 git 存储库

*   从远程 git 存储库通过`git clone`在目标服务器上克隆任务

*   最后运行任务以应用所需的配置

与`git push / git pull`方法相比，这看起来是额外的步骤，但是给了你灵活性和力量。

*   复杂的配置被分割成更容易管理的微小部分。

*   因为 sparrow 任务不仅仅是配置，还可能运行配置服务器所需的额外步骤(安装包等等)

# 让我们来看看这是怎么回事

为了简单起见，我将我的帖子限制在两个简单的用例上。

*   配置 nano 编辑器(一些基本设置)
*   配置 git 全局设置(名称和电子邮件)

Sparrow 自带了很多插件，不需要编码什么，用已经存在的就行:

```
$ sparrow index update # get the latest updates of sparrow repository
$ sparrow plg install nano-rc
$ sparrow plg install git-base 
```

Enter fullscreen mode Exit fullscreen mode

现在已经安装了插件，让我们创建几个任务来反映所需的设置。

对于 git 设置:

```
$ sparrow project create git
$ sparrow task add git setup git-base
$ sparrow task ini git/setup

install_git: on 
email: melezhik@gmail.com
name: Alexey Melezhik 
```

Enter fullscreen mode Exit fullscreen mode

对于纳米配置文件:

```
$ sparrow plg install nano-setup
$ sparrow task add nano rc nano-setup
$ sparrow task ini nano/rc

tabsize: 2
speller: hunspell -x -c 
```

Enter fullscreen mode Exit fullscreen mode

如果我们现在列出当前的任务，我们可以在这里看到它们:

```
sparrow root: [/home/suslik/sparrow]
[sparrow task list]
 [utils]
  git/setup
  nano/rc 
```

Enter fullscreen mode Exit fullscreen mode

# 保存任务到 git 仓库

工作流程的其余部分类似于将 dotfile 保存到 GitHub 存储库。我们将使用`sparrow task save`命令将任务保存到目录中，然后将文件推送到 git:

```
$ mkdir tasks

$ git init .

$ git remote add origin https://github.com/user/tasks.git

$ sparrow task save $PWD

$ git add . && git commit -a -m "my sparrow tasks" && git push 
```

Enter fullscreen mode Exit fullscreen mode

# 从 git 仓库恢复任务

一旦任务被保存到 git 库，我们就可以在任何服务器上安装它们:

```
$ git clone https://github.com/user/tasks.git my-tasks

$ sparrow task restore my-tasks 
```

Enter fullscreen mode Exit fullscreen mode

我们都准备好了。要开始使用我们的任务，只需运行:

```
$ sparrow task run git/setup # set up my git client
$ sparrow task run nano/rc # configure nano editor 
```

Enter fullscreen mode Exit fullscreen mode

最后，这是我的[任务库](https://bitbucket.org/melezhik2/tasks/src/master/)——作为一个例子来看一下。

* * *

感谢您的阅读。