# 为什么这 5 个关于 Linux 命令行的神话是错误的

> 原文：<https://dev.to/xeroxism/why-these-5-myths-about-the-linux-command-line-are-false-i3a>

[![linux_terminal_fossnaija](../Images/3bcbddef6308c514ec3ed5cc6fe0c929.png)T2】](https://i1.wp.com/fossnaija.com/wp-content/uploads/2018/02/linux_terminal_fossnaija.png?ssl=1)

图片:化石人. COM

使用[命令行](https://fossnaija.com/the-anatomy-of-the-linux-command-line/) (CL)让许多计算机用户感到害怕，尤其是那些使用 [Linux](https://fossnaija.com/linux-at-25-from-grass-to-grace/) 的用户。所有的努力都是为了尽可能避免它。这种趋势的一个主要原因是，许多这样的用户来自于使用操作系统(OS ),在该操作系统中，重要的事情是以点击的方式仅使用鼠标来完成的。我个人能对那段经历产生共鸣。当我第一次开始使用 Linux 时，我也很害怕。我一直习惯于 Windows——事实上有一段时间[我认为“操作系统”是 Windows](https://fossnaija.com/5-reasons-why-a-nigerian-might-not-use-linux/) 的别名。有充分的理由；那是当时我唯一可用的操作系统。我从来没有考虑过使用 windows 命令界面(*cmd.exe*)。

可悲的是，这就是今天许多 Linux 新手的感受。每个 Linux 用户都应该熟悉 CL，这一点很重要，因为许多使 Linux 功能强大的工具都需要使用 CL。

在这篇文章中，我将揭穿一些关于 Linux 命令行的神话；让您将 Linux CL 视为朋友而非敌人。

让揭穿开始…

## 误区一:能用它做的事，不用它也能做。

许多 Linux 发行版(简称为\**distro**_)现在以桌面环境(DE)和主题的形式捆绑了令人惊叹的点击友好的图形用户界面(GUI)。但是这并没有使 CL 的有用性过时。并不是所有可以用 CL 完成的事情都可以用 GUI 来完成。掌握 CL 会增加你的工作流程(你在电脑上完成任务所需的时间),同时节省你的宝贵时间。

CL 使你能够做非常强大的事情。让我们来看看在更改文件中的访问权限时如何看到这一点。假设您希望更改对文件的[访问权限。为了使用 GUI 完成这个](https://fossnaija.com/assigning-and-removing-file-access-permissions/)[，步骤](https://fossnaija.com/file-and-directory-permissions-using-the-gui/)可能包括:

1:打开包含文件的目录/文件夹

2:右键单击文件并从下拉菜单中选择属性。

3:选择“权限”选项卡。

4:将“所有者”类别的权限访问更改为读写。

5:将“组”类别的权限访问更改为读写。

6:将“其他”类别的权限访问更改为读写。

[![permission_settings_fossnaija](../Images/cfb2574fbdb57274cc450af207bb136c.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/02/permission_settiings_fossnaija.png?ssl=1)

图片:化石人. COM

使用命令行，您可以通过键入以下命令获得相同的结果:

sudo chmod-R-v 777/路径/到/目录/包含/文件/

这是 CL 的一个步骤，而不是使用 GUI 的一个文件的六个步骤。与 GUI 不同，使用命令是可伸缩的；无论目录中有多少文件(比如 1000 个)，它仍然是那一行命令。但是你会想要手动(点击)改变一千个文件的访问权限吗？这将需要总共 6000 个步骤。

这是你需要开始使用 CL 的最有说服力的原因之一——它会节省你很多时间。

你明白了，对吧？

## 神话 2:CL 是为极客准备的。

这个已经很老了，尤其是考虑到 Linux 是极客或“黑客”的产品。最近，在开发吸引人的桌面体验方面已经有了很多进步，从而打破了这个神话。Linux(在不同的发行版中)现在看起来和感觉上类似于迄今为止被认为非常用户友好的操作系统。命令行可以通过自定义背景图像、字体类型和颜色等方式配置成不同的外观。

[![Terminal_color_settings_fossnaija](../Images/61f1966a25924bb75e538e069a874c5b.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/02/Terminal_color_settings_fossnaija.png?ssl=1)

图片:化石人. COM

CL 不是关于谁是极客，而是关于快速完成任务。在这个过程中节省了大量时间。通过掌握常用的命令，CL 可以用来执行常规的计算机操作，而不一定是“极客”或“程序员”。

## 误解 3:使用 CL 会损害你的电脑。

的确，Linux CL 提供了对计算机系统的直接访问。但有内置的机制来确保未经授权的访问计算机被阻止。一个重要的方法是，即使一个未经授权的人获得了访问您的 CL 终端，大多数严重的命令(可能对您的 PC 造成损害的命令)都需要系统所有者的密码才能执行。Linux 系统中的另一个特性是[文件权限。](https://fossnaija.com/assigning-and-removing-file-access-permissions/)根据所授予的权限数量，执行一些文件操作，如打开(读取)、修改(写入)和运行(执行)，需要密码(来自系统所有者)。

## 误区四:你需要知道所有的命令。

真的吗？这是不可能的，更谈不上实用。有数百个可用的命令，而且还在不断创建更多的命令。期望您“记住”或掌握这些命令以便能够使用 Linux CL 是不合理的。只有大约十几个或者稍微多一点的命令会被经常使用；这些才是你需要关注的。事实上，你使用的命令越多，就越容易记住。CL 是使用命令来完成工作，对你来说重要的是知道没有必要担心去了解所有对你的生产力没有用的命令。

当你不确定一个命令或者如何使用它的时候，CL 中有很多资源可以帮助你。这将在下一点中讨论。

## 误区 5:没有命令协助或文档。

Linux 中有许多 CL 辅助工具，可以确保您获得关于不同命令如何工作以及如何使用它们的相当平衡的知识。例如， *man* 页面可用于 Linux 命令。手册页简要介绍了命令功能和选项。例如，要检查如何使用“cat”命令，只需在 [Linux 终端](https://fossnaija.com/the-anatomy-of-the-linux-command-line/)中键入以下内容:

男人聊天

[![cat_command_man_page_fossnaija](../Images/33e6263645cd9c0caf94cc2571ed657d.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/02/cat_command_man_page_fossnaija.png?ssl=1)

图片:化石人. COM

还有一个命令“帮助”功能，大多数命令都有这个功能，您可以查看。例如:

卡特彼勒–帮助(或卡特彼勒–h)

[![cat_command_help_page_fossnaija](../Images/47917c6fbfd511b625689aae6ede735d.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2018/02/cat_command_help_page_fossnaija.png?ssl=1)

图片:化石人. COM

当一个错误的/不恰当的命令被输入到终端时，Linux 会给出一个反馈，这个反馈可以包含拼写相似的命令选项(试图猜测您的意图)或者正确的命令组合。

[![wrong_command_output_fossnaija](../Images/97819bf67cce5e8f6ab254d99484f8f4.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2018/02/wrong_command_output_fossnaija.png?ssl=1)

图片:化石人. COM

现在就熟悉 Linux 命令行吧！没什么好怕的。记住微小的水滴汇成海洋。

保持敬畏和；

快乐的 Linux！

帖子[为什么这 5 个关于 Linux 命令行的神话是错误的](https://fossnaija.com/5-myths-linux-command-line-false/)首先出现在 [Foss Naija](https://fossnaija.com) 上。