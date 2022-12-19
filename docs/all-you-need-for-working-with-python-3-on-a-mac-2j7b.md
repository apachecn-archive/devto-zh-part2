# 在 Mac 上使用 Python 3 所需的一切

> 原文：<https://dev.to/datamatin/all-you-need-for-working-with-python-3-on-a-mac-2j7b>

[![](../Images/7abbb49daa691f0ce45723051bbb36ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fMCRF5X9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFyEkUvuwlT7IGHVtmD9sjA.png)

为了澄清关于在新 Mac 上开始使用 Python 3 的功能设置的任何困惑，这里有一个快速(最简单)的指南。

### DO #1:下载并安装 Python 3.x.x

去[官网](https://www.python.org/downloads/)从那里下载 Python。是的——既然你问了——**一定要最新版本的**(有闪亮黄色按钮的那个):

[![](../Images/c554ee5c34fa31f1abd210d66cf8021d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QxGTHEy2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AX5oLo0KLz4XZ6YQc9y5psA.png)

执行安装程序并逐步完成所需的步骤…

<figure>[![](../Images/3969f7df1bea1a922d5c779a772ce300.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ttPoRE7l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/549/1%2A3Q0aCaGwIIZvZjo5lwCSDg.png) 

<figcaption>(比那容易)</figcaption>

</figure>

如果你在 Mavericks (10.9)或以上，你(几乎)完成了。

#### > >真的吗？证明它！

打开您的终端，输入
进行验证

```
python3 --version 
```

Enter fullscreen mode Exit fullscreen mode

它应该会用类似于 *Python 3.7.0* 的内容来回应您

然后键入:

```
git --version 
```

Enter fullscreen mode Exit fullscreen mode

这应该:

*   提示您安装命令行工具。如果是这样，允许安装(它将把版本控制带到您生活中！).顺便说一下，这是从上面完成“(几乎)”的步骤。
*   或者，如果它已经存在，它会说类似于: *git 版本 2.15.2(苹果 Git-101.1)*

最后，您还可以向您的患者终端询问以下内容:

```
pip3 --version 
```

Enter fullscreen mode Exit fullscreen mode

并且它会尽职尽责地用一个冗长的:*pip 18.0 from/Library/Frameworks/python . framework/Versions/3.7/lib/python 3.7/site-packages/pip(python 3.7)*

至此，您已经准备就绪，可以开始使用 Python 3 编写代码**，使用 **pip3** ( [创建一个**虚拟环境**](https://medium.com/@martin.breuss/virtual-environments-for-python-cc8f18a48c6c)**首先，在上安装您可能喜欢的任何包**！)，和 **git** 一起沉迷于**版本控制**的安心美。**

 **#### > > >你的 OS 比 10.9 老怎么办？

更新一下。它是免费的，可以让你的电脑变得更好。

如果出于某种原因你不能更新你的操作系统，但仍然想继续使用 Python 3 和 git，那么[从这里获取 Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git):

> 在 Git 网站[http://git-scm.com/download/mac](http://git-scm.com/download/mac)有一个 macOS Git 安装程序可供下载。

[![](../Images/0736d24ac701271dfcc4a998d338bc37.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JUtNo1F0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ADseohaM183bB7_Zh)

> 你成功了！干得好！现在，为了让你开心，这里有一个在教程中经常提到的关于如何开始的步骤列表，但是在我看来，它们只会使基本的设置变得复杂。

### 不要#1:安装蟒蛇

Anaconda 是一个伟大的数据分析工具，然而，它和[一起带来了很多东西](https://stackoverflow.com/a/42096429/5717580)。Anaconda 是一个成熟的 Python *发行版* (=它是自己的*版本*)Python！)，它带有自己的包管理器，预装了大量的数据科学包，哦，我提到过它还包括 *R* 吗？！

### 不要#2:安装自制软件

每隔一个教程就告诉你先安装 homebrew，然后用它在 mac 上获得 python。不要这样做。没必要。

我个人不太喜欢酒窖，尽管几年前我尝试过自制(我只是为了体验而喝，不是为了品尝；)单凭这一点还不足以让我接受家酿啤酒的酒窖结构在我的机器上诱发的容易被遗忘和可能早已被遗忘的残留物。

### 不要#3:安装 pip

如果你从官方下载页面下载 Python 3，你就不必强制安装 pip。它是自愿出现的。

[官方 pip 站点](https://pip.pypa.io/en/latest/installing/)发言:

> 如果您使用的是从[python.org](https://www.python.org/)下载的 Python 2 >=2.7.9 或 Python 3 >=3.4，则已经安装了 pip

[![](../Images/358286873646f8f16faa9c4b1ca493e9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JUkgquWo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIauQ9ox4OojpggX1sHwGjA.png)

#### +/-有用教程

*   [https://docs.python.org/3/using/mac.html](https://docs.python.org/3/using/mac.html)
*   [https://itsevans.com/install-pip-osx/](https://itsevans.com/install-pip-osx/)
*   [https://www . digital ocean . com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-MAC OS](https://www.digitalocean.com/community/tutorials/how-to-install-python-3-and-set-up-a-local-programming-environment-on-macos)
*   [https://docs.python-guide.org/starting/install3/osx/](https://docs.python-guide.org/starting/install3/osx/)
*   [https://www.stuartellis.name/articles/mac-setup/](https://www.stuartellis.name/articles/mac-setup/)
*   [https://realpython.com/python-first-steps/](https://realpython.com/python-first-steps/)

#### 其他资源

*   [https://pip.pypa.io/en/latest/installing/](https://pip.pypa.io/en/latest/installing/)**