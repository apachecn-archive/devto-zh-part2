# Git 子模块再探

> 原文：<https://dev.to/dwd/git-submodules-revisited-1p54>

Git 的子模块受到如此普遍的嘲笑，以至于几乎整个行业都致力于提供管理依赖关系的替代方案。

但是像 git 中的任何东西一样，通常值得仔细查看一下手册页，弄清楚是否有一些选项符合您的要求，或者看看它们最近是否有所改进。

## 我想要什么

所以，[米](https://github.com/surevine/metre)是我的示范项目。它有许多子模块，部分是因为我们的一些客户运行(真的)旧版本的 Linux，所以我们需要静态链接。耶，有趣！

例如，这意味着管理和发布我们自己的 OpenSSL 版本——对于我们的安全人员(可爱的西蒙)来说，这是一个可怕的前景。实际上，我也很害怕。

实际上，我们的发布周期包括在所有子模块上沿着一个稳定的分支前进，因此我们确信我们已经找到了任何错误修正。这需要尽可能简单——实际上，我们可以根据需要运行一个命令。

但是，我们希望有很高的信心，检查一个特定的 met 提交散列将会给我们提供与我们构建时相同的依赖关系。

## Git 子模块添加

最初，我去做`git submodule`和很多手工工作。我(首席开发人员)对此并不满意。保安西蒙对此很不高兴。Pete 是我们的高级开发人员之一，他对这个项目进行了全面的审查，并强调了这一点。

问题是，一个失误和一个依赖可能会给。米意味着所有关于安全。

`git submodule`的优点是它跟踪子模块的提交散列，你可以用`git clone --recursive`或`git submodule update --init --recursive`在正确的散列检查它们。

我们考虑过换成其他东西，但那样我们会失去很多`git submodule`的内置智能，这也是一种痛苦。

## 哦，看——树枝！

然而，深入研究`man git-submodule`和`man 7 gitsubmodules`，我发现了黄金。

首先，有一个`-b branch`切换到`git submodule add`。这在一个特定的分支添加了子模块，此外还将“跟踪分支”git 通常从中提取的分支——设置为远程源分支，就像您通常所做的那样。

第二，我找到了一个配置选项`submodule.{submodule name}.branch`，它存储了这个。不过，这并没有您想象的那么好，因为虽然您可以在库的`git config`中设置它，但它并没有被跟踪。

## 敬畏我的编辑斯基尔兹

然而，子模块配置存储在储存库顶部的`.gitmodules`文件中。因此，您可以编辑该文件，找到该部分，并简单地在那里添加一个`branch`键:

```
[submodule "deps/spiffing"]
        path = deps/spiffing
        url = http://github.com/surevine/spiffing
[submodule "deps/openssl"]
        path = deps/openssl
        url = git://git.openssl.org/openssl.git
        branch = OpenSSL_1_1_0-stable 
```

Enter fullscreen mode Exit fullscreen mode

不过，缺省值是`master`，所以如果这就是你想要的，你已经得到了。

## 更新分店

更新子模块的常规命令是`git submodule update`。有三个有趣的标志:

如果子模块没有被克隆到位，那么`--init`执行一个`git submodule init`,否则不执行任何操作——所以使用起来总是安全的。

`--recursive`遍历每个子模块，在每个子模块中运行相同的`git submodule update`命令。

`--remote`是沿着远程跟踪分支执行一个`git pull`的魔术。这就是我们想要的。

## 工作流程总结

所以现在的工作流程是这样的:

`git clone --recursive git@github.com:surevine/metre` -克隆存储库并签出`master`的`HEAD`。

检查 foo 分支或提交，并将子模块切换到它们用于 foo 的提交。

`git submodule update --init --recursive --remote` -沿跟踪分支递归更新*所有*子模块。如果没有`--remote`，它会将子模块工作目录重置为父模块的“正确”提交。

最后，您可以:

`git config submodule.recurse true` -告诉 git 大多数命令应该递归执行，尤其是`git pull`。