# 请看 z，这颗重新思考外壳导航的无名宝石

> 原文：<https://dev.to/aymericbeaumet/behold-z-the-unsung-jewel-that-rethinks-shell-navigation-314a>

* * *

*为了更好的阅读体验，请阅读最初发布的文章:[https://aymericbeaumet . com/looke-z-the-un sung-jewel-that-rethinks-shell-navigation](https://aymericbeaumet.com/behold-z-the-unsung-jewel-that-rethinks-shell-navigation)*

* * *

我花了相当多的时间在候机楼里闲逛。很可能你也是，因为你敢于公开这个故事。我的日常活动引导我为各种项目做出贡献(专业的、个人的和开源的)。为了我的理智，我倾向于遵循严格的规则来组织我的工作空间，这有助于我保持东西整洁并且容易到达。至少我是这么认为的。

我决定按照 [Go 哲学](https://golang.org/doc/code.html#Workspaces)来安排我的项目:让树状结构反映它们可到达的 URL。例如，`~/Workspace/src/github.com/aymericbeaumet/dotfiles`将包含由 [@aymericbeaumet](https://github.com/aymericbeaumet) 拥有并可在[github.com](https://github.com)访问的项目[点文件](https://github.com/aymericbeaumet/dotfiles)。这很好，但是我现在面临着一个复杂的目录结构，很难快速浏览。最终，让我更难获得我需要的资源。

```
$ pwd
/Users/aymericbeaumet/Workspace/src/github.com/brigad/back-end 
```

Enter fullscreen mode Exit fullscreen mode

没有人想手动处理这个问题。

记住并键入每个项目的位置(即使在利用自动完成功能时)是非常耗时的。创建外壳别名是不可伸缩的。相反，我希望依靠每个项目名称的伪唯一性在我的计算机上的所有目录中找到它。

## z？

有几种工具可以解决这个问题。我最喜欢的是 [*z*](https://github.com/rupa/z) 。它遵循 Unix 的理念，小巧、专注、可互操作。 *z* 允许快速跳跃，自动学习你的习惯。条目是根据你访问目录的[频率](https://en.wikipedia.org/wiki/Frecency)来排序的。

让我们浏览一下这个工具的基础知识。下面是我建议如何在 macOS 上为 Zsh 安装它(其他 shells 和操作系统的安装过程应该类似):

```
$ brew install z
$ echo "source '$(brew --prefix z)/etc/profile.d/z.sh'" >> ~/.zshenv 
```

Enter fullscreen mode Exit fullscreen mode

*在 macOS 上为 Zsh 安装 z。*

注意安装 *z* 并不足以开始使用它。您必须在每个 shell 会话开始时找到`z.sh`来激活它。配置文件`~/.zshenv`是合适的，因为 Zsh [在大多数情况下都会加载它](http://zsh.sourceforge.net/Intro/intro_3.html)，使得该工具可以在各种各样的环境中访问(终端、编辑器、ide、脚本等)。).

## 我们来玩吧

一旦安装和设置完成:开始一个新的 shell 会话，`cd`通过一些文件夹，并执行`z`以确认它已经准确地跟踪您的活动:

```
$ z | sort -rn | head -3
5331.72    /Users/aymericbeaumet/Workspace/src/github.com/brigad/back-end
637.255    /Users/aymericbeaumet/Workspace/src/github.com/brigad/ops
386.198 /Users/aymericbeaumet/.config/dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

*基于频率的前 3 个目录。*

如上所示，我花了相当多的时间浏览[旅](https://github.com/brigad)项目，但似乎我也经常访问我的[点文件](https://github.com/aymericbeaumet/dotfiles)来调整一些配置。排名分数是不稳定的，人们可以在短时间内观察到显著的变化。

现在 *z* 已经积累了一些知识，试着导航到其中一个被跟踪的文件夹。导航非常简单，只需执行`z`命令，后跟目录名:

```
$ z dotfiles
$ pwd
/Users/aymericbeaumet/.config/dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

*匹配整个目录名导航。*

事实上，我更喜欢利用部分匹配(想象一下您的搜索模式周围的`**`)来输入更少的字符。因此，`z dot`是有效的，应该产生类似于`z dotfiles`的行为(对可能的名称冲突取模，但是你得到了想法)。这是因为 *z* 依赖于正则表达式。例如，下面的代码片段将导航到以字母“tmp”(最高分)结尾的目录:

```
$ z tmp$
$ pwd
/private/tmp 
```

Enter fullscreen mode Exit fullscreen mode

*通过匹配正则表达式进行导航。*

整洁对吗？我猜你开始看到这个工具的可能性。我们只是触及了它能力的表面。我建议你深入研究一下它的文档,了解它的全部潜力。诸如自动完成、子目录匹配、连续匹配等功能。是游戏规则的改变者。

## 结论

z 是巨大的。这个工具每天都为我节省了大量的时间。当一个人开始依赖它，就没有回头路了。我希望它能适合你我的工作流程。

如果能听到你任何节省时间的诀窍，我将不胜感激。欢迎分享你的经历！