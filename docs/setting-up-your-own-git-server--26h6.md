# 设置您自己的 Git 服务器

> 原文：<https://dev.to/erhankilic/setting-up-your-own-git-server--26h6>

这篇文章最初发表于[建立你自己的 Git 服务器](http://erhankilic.org/post/setting-up-your-own-git-server/)

设置自己的 git 服务器非常有用。您可以在 Vps、Vds、专用服务器甚至本地机器上设置自己的 git 服务器。我假设您已经在 Linux 服务器上安装了 git。

首先我们需要在根目录下创建一个 git 目录。在下面的代码中，我将创建一个名为 git server 的示例。所以您可以设置任意多的 git 服务器。

```
mkdir /git/example.git 
```

Enter fullscreen mode Exit fullscreen mode

然后在终端中，转到目录 **/git/example.git** ，编写下面的代码。

```
git init --bare 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！！我们创建了我们的 git 服务器。如果你想克隆你应该写下面的代码；

```
git clone username@serveripaddress:/git/example.git 
```

Enter fullscreen mode Exit fullscreen mode

它会询问你的服务器的密码。

如果您的计算机上有一个本地项目，并且您希望将它包含在您的 git 服务器中，请在终端的项目目录下键入以下代码；

```
git remote add myserver username@serveripaddress:/git/example.git
git push myserver -u master 
```

Enter fullscreen mode Exit fullscreen mode

您可以随意更改**“my server”**的名称。**【产地】**是常用名称。

当您使用 push 命令发送您的提交时，您的服务器将询问用户密码。

如果你想让你的服务器接受你的提交，并在你发送提交时在另一个目录下创建你的项目，你必须用 **777** 权限在 **/git/example.git/hooks** 目录下创建一个**接收后**命名的文件。你为什么想要这样的东西？在您的项目中，我们假设您有像 gulp 这样的构建操作的命令。在发送提交之后，您可能希望 gulp 过程自动运行，并且您的项目将准备就绪。您甚至可以让开发和生产分支在不同的文件夹中自动运行。

如下编辑**接收后的**文件的内部。

```
#!/bin/bash
GIT_WORK_TREE=/home/project-directory/ git checkout -f master 
```

Enter fullscreen mode Exit fullscreen mode

您可以随意设置目录位置。

如果你用的是 composer，bower 之类的东西，可以安排他们如下工作。

```
#!/bin/bash
GIT_WORK_TREE=/home/project-directory/ git checkout -f master
cd /home/project-directory/
php composer.phar update
cd /home/project-directory/
bower install 
```

Enter fullscreen mode Exit fullscreen mode