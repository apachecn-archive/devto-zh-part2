# 你的壳程到底是什么？

> 原文：<https://dev.to/jalendport/what-exactly-is-your-shell-path-570p>

最近，我试图向一个朋友解释他电脑上的 PATH 变量的用途。在我们交谈的 Slack 小组里写了一篇很长的文章后，我意识到这是写博客的好材料。所以现在开始…

你可能已经听到人们和/或博客帖子谈论编辑你的路径或“某某”不存在于你的路径中。你就像这样，*“等等，这条路到底是什么？”*。好问题！

> PATH 变量基本上是一个目录列表，您的计算机通过它来查找请求的可执行文件。

如果这个定义在这一点上没有太多意义，请继续阅读，我将深入解释它是如何工作的…

<figure>[![](img/a9d20054336e147a1812f003864f0327.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u1B5ObFa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aa3YcNkv_QRWTvzhBhsS-WQ.jpeg) 

<figcaption>你的路径相当于一堆方向牌，告诉你的电脑往哪里走</figcaption>

</figure>

#### 文件系统简介

让我们从 1️⃣.广场开始您的计算机由文件组成；其中有两种类型。你有数据文件(只包含信息的文件)和可执行文件(用于在计算机上执行各种功能和操作的文件)。

每当你打开一个计算机应用程序，你本质上只是在运行它的可执行文件。这同样适用于您在终端中使用的命令——例如，cd 和 ls 只是可执行文件。这些可执行文件通常存储在一些特定的目录中。例如，应用程序的可执行文件存储在应用程序文件夹中。所有 CLI 程序/命令的可执行文件通常存储在以下几个位置之一:

*   `/bin` → **用户实用程序** —包含一些常用的可执行文件，用于通过命令行(cd、ls、rm 等)导航目录和管理文件
*   `/sbin` → **系统程序和管理实用程序** —包含可执行文件，用于处理系统的引导、恢复、复原和/或修复(启动、重启、挂载等)
*   `/usr/bin` → **通用工具、编程工具和应用** —包含对系统功能不重要但仍包含在操作系统中的可执行文件(ssh、php、vim 等)
*   `/usr/sbin` → **系统守护进程&系统实用程序** —包含通常作为后台进程运行的可执行文件(cron、php-fpm、coreaudiod 等)
*   `/usr/local/bin`、`/usr/local/sbin` → **用户安装的可执行文件、库等。** —包含与上述文件夹不同的可执行文件，默认情况下不包含在操作系统中(自定义外壳脚本，以及通过[自制软件](https://brew.sh/)安装的程序，随特定 GUI 程序安装并需要的实用程序等)。)

除了这些标准目录，一些程序使用自己的

`/bin`类目录来存储可执行文件。例如， [Homebrew](https://brew.sh/) ，一个流行的 macOS 软件包管理器，把它安装的任何可执行文件放在`/usr/local/Cellar`中(然后这些可执行文件被[符号链接](https://en.wikipedia.org/wiki/Symbolic_link)到你的`/usr/local/bin`文件夹中以便于使用)。 [Composer](https://getcomposer.org/) ，PHP 的依赖管理器，在`~/.composer/vendor/bin`中安装可执行文件。取决于你如何安装 MYSQL ，它的一些实用程序(例如 mysqldump)被安装在`/usr/local/mysql/bin`。

#### 一切关于路径

因此，我们现在已经将可执行文件散布在我们的文件系统中的这些不同的文件夹中。我们现在可以在终端中通过键入文件的完整绝对路径来访问和使用这些可执行文件。因此，例如，如果您想使用 ls 列出当前目录的文件，您可以在终端中运行

`$ /bin/ls`。或者如果你在`/usr/local/bin`中安装了一个很棒的程序，你可以运行`$ /usr/local/bin/my-program`来访问它。但是您真的希望每次都必须输入可执行文件的完整路径吗？难道你不想跑`$ ls`或者`$ my-program`吗？

> 🤔**记住:** *不要在命令的开头复制/粘贴美元符号！它类似于您终端中的提示符，它只是让您知道这个命令应该在终端中运行。*

这就是 PATH 出现的原因！这里有一个关于路径如何工作的类比:假设你是几个大型仓库的经理。在每个仓库里，你都有一堆不同的产品。你也有一个电子表格或地图，列出每个仓库的位置和每个仓库储存的产品。

PATH 以类似的方式工作——它是一个全局变量，包含一串由`:`分隔的不同路径。当您键入一个程序的名称而不使用绝对路径时，您的计算机就使用这个变量来理解它应该在什么目录中查找您所请求的可执行文件。从列表中的第一个目录开始，它将搜索与您所请求的相匹配的目录，这意味着路径中第一个出现的目录优先于列表中后面的目录。

> 🔥**提示:** *如果你想看看你的路径当前设置为什么，运行* `$ echo $PATH`。

<figure>[![](img/6fd90f94a8b6f25a471507b6427b4733.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---QNWJfye--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2CL8TDXZRodXpOjT_PNC3g.jpeg) 

<figcaption>路径中的一个目录=一个仓库📦</figcaption>

</figure>

我打开一台全新安装了 macOS 的 Mac，在那台机器上,“默认”路径似乎是`/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin`。所以开箱即用，你的 Mac *应该*自动使用该路径来满足安装在默认/标准 bin 位置的任何程序的请求。但是，如果您正在使用另一个工具，如 Composer 或 Homebrew，并希望将其他目录添加到您的路径中，该怎么办呢？

#### 编辑你的路径

由于 PATH 本质上只是一个变量，我们可以在命令行或脚本中使用`$PATH`或`${PATH}`来访问 PATH。当您想要构建类似于`$ echo ${PATH}foo`的东西时，`${PATH}`形式尤其有用，因为运行`$ echo $PATHfoo`会试图寻找一个名为 PATHfoo 的变量。

例如，假设我们想要添加一个优先于当前已添加目录的新目录(即，我们想要将其添加到其他目录之前)，我们可以在终端中运行以下命令:

```
$ export PATH="/my/directory/bin:$PATH" 
```

现在，当我们运行`$ echo $PATH`时，您应该看到您的新目录列在路径中其他目录之前(类似于`/my/directory/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin`)。

但是也许你想把这个目录添加到路径的末尾，这样它就是最后一个要搜索的目录。然后你可以这样做:

```
$ export PATH="$PATH:/my/directory/bin" 
```

你的新路径将看起来像`/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/my/directory/bin`。

干得好！🎉您刚刚成功编辑了您的路径。

但是，您刚才对路径所做的更改不会持续下去；换句话说，下一次你注销或重启你的 Mac，你会回到原来的路径。我们希望对路径的更改能够持久，所以我们要么必须编辑`/etc/paths`文件，要么将自定义路径声明添加到本地 shell 文件中。

如果我们编辑`/etc/paths`文件，路径更改将在系统的所有用户中全局应用。如果您在文本编辑器中打开`/etc/paths`，您会注意到有一堆目录列在单独的行上。要添加您的自定义目录，请在文件中的任意位置创建一个新行，并添加您的目录路径(文件中位置较高的目录优先于下面列出的任何目录)。但是，如果您只是想为您的本地用户改变这一点，我建议您只需在本地 shell 文件中添加必要的行。

如果您正在使用 Bash(大多数 UNIX 系统上的默认 shell)，您可能想要将它添加到您的`~/.bash_profile`或`~/.bashrc`文件中。如果你正在使用另一个 shell，你可以把它添加到 shell 使用的任何文件中(例如 ZSH 使用了一个`~/.zshrc`文件)。基本上，你需要做的只是在文本编辑或代码编辑器中打开适当的文件，在文件的某个地方添加你需要的自定义路径声明。例如，我的`~/.bashrc`包含以下行:

```
export PATH="/usr/local/bin:/usr/local/sbin:$PATH"
export PATH="$PATH:~/.composer/vendor/bin" 
```

添加必要的导出后，保存文件并退出，然后重新启动“终端”来载入新的更改。

[![](img/cb707d3950acf7aab5e16390e99c4ad0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PZsv8EGH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AC_QcFA7ajZGPhZaujbW5ug.jpeg)

#### 实际例子

我经常听到的一个与路径相关的常见问题是，某些应用程序或工具抱怨找不到 mysqldump 可执行文件(MYSQL 中安装的一个数据库备份实用程序)。因为它找不到这个可执行文件，所以它会挂起它试图运行的任何任务，并向您抛出错误代码。正如我前面提到的，MYSQL 有自己的类似于`/bin`的目录，其中安装了 mysqldump 之类的实用程序。

所以我们要做的是将这个 MYSQL /bin 文件夹的路径添加到我们的 path 变量中，这样我们尝试使用的这个工具将能够访问 mysqldump 来完成任务。我们将选择编辑我们的 shell 文件，因为目前我们不需要将这个路径更改全局应用到计算机上的所有用户。此外，我们在这台特定的计算机上使用 BASH。

让我们继续，在文本编辑器中打开`~/.bash_profile`并添加下面一行:

```
export PATH="$PATH:/usr/local/mysql/bin" 
```

接下来，我们需要保存文件，关闭我们的文本编辑器，然后退出并重新启动终端，这样新路径就会生效。

现在，我们应该能够重新运行我们之前尝试完成的任务，它应该能够在路径中找到 mysqldump。

希望现在你已经很好的掌握了你的道路是什么以及它是如何运作的！

* * *

如果您在以上任何方面有问题，或者仍然试图理解 PATH 是如何工作的，请联系我，我会尽力帮助您。你可以在下面留言或者在 Twitter 上联系我( [@jalendport](https://twitter.com/jalendport) )。

如果你觉得这篇文章很有帮助或者喜欢阅读，请在这里或者在[的推特](https://twitter.com/jalendport)上关注我，看看以后的文章！

* * *

*本文原载于[媒体](https://medium.com/@jalendport/what-exactly-is-your-shell-path-2f076f02deb4)。*