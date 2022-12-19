# 📺全屏 Xcode 和模拟器

> 原文：<https://dev.to/zdnk/-fullscreen-xcode-and-simulator-42ng>

您在应用程序、虚拟桌面或全屏应用程序之间切换的方式会影响您的工作流程、效率和专注度。我有一个小技巧，如何在分开的全屏模式下运行 Xcode 9 或 10，这样你就可以专注于开发而不会分心。

**这篇文章也发表在[我的博客](https://blg.zdnkt.com/fullscreen-xcode-and-simulator/)上。**

# ☹️的问题

我不知道你怎么想，但是我真的很喜欢在全屏下使用 Xcode，或者其他 IDE(和不同的应用程序)。它让我充分利用了屏幕，我可以轻松地在应用程序中变得更有效率。此外，由于 macOS 手势，在两个或更多应用程序之间切换更容易。但是有一件事曾经是我的一个问题。它正在调试应用程序。你有两个选择:

1.  真实设备
2.  或者模拟器

对于真正的设备，你必须一直拿起它，甚至可能解锁 Xcode 才能运行应用程序。真烦人。我使用这种方法很多年了，因为我认为“没有什么像设备一样”，后来我变得聪明了一点，开始使用 Simulator 进行我的大部分测试和调试。它进行得很好，但是有一些棘手的问题:

*   每次我都必须用手势切换到模拟器(然后再切换回来)
*   当 Xcode 遇到断点或应用程序崩溃时，macOS 会将我转换回 Xcode
*   你不能同时查看应用程序和控制台输出

过了一会儿，a 意识到这让我很困扰，没有效率，我多次失去了注意力。

# ✅解决方案

一；一个💡我脑子里突然冒出一个想法，为什么不全屏放在 Xcode 旁边呢？不幸的是，模拟器默认不支持全屏模式。我做了一点研究，发现有一种方法，但这不是我们所需要的，如果我们有一个全屏运行模拟器，第二个运行 Xcode，这几乎不会有任何改善。我们需要将它们合并在一起，以分开的全屏模式运行。幸运的是，一旦它们支持全屏，这两者都可以实现。

[![Xcode 10 with Simulator in fullscreen](../Images/41acfcb81cae864c7fb1e2f1fa6236c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzHwktr1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blg.zdnkt.com/content/images/2018/08/Screenshot-2018-08-02-at-20.27.34.png) 
左边是全屏运行的 Xcode 10 beta 5，右边是运行 iOS 12 的 iPhone X 模拟器。

# 💨简单的方法

要在模拟器中启用全屏模式，只需打开终端应用程序并**运行以下命令**:

```
defaults write com.apple.iphonesimulator AllowFullscreenMode -bool YES 
```

**就是这样！**

如果你想检查其他设置(选项在下面的截图中)，你可以在模拟器中更改，继续阅读并按照步骤操作。

# 📐更多自定义

如果你想为 Xcode 9 这样做，那么你很好，T2 就是你所需要的全部。然而，如果你已经在使用 **Xcode 10** ，你**需要 Xcode 的两个版本**(9 和 10)才能工作。

苹果在整个系统及其应用程序中都隐藏了功能和设置。当您在主卷的根目录中有一个名为`AppleInternal`的目录时，您可以访问其中的一些。

## ❓什么是`/AppleInternal`？

AFAIK 它是一个空目录，Apple 工程师使用它来启用开发和调试功能、菜单和设置。当你创建目录时，一堆`Internal`菜单可能会出现在不同的应用程序中，我建议忽略它，除非你知道你在做什么。我们将使用它来启用模拟器应用程序的全屏模式。

## 👣步伐

让我们开始吧！

### 1.检查您是否需要禁用 SIP

就**试**做**第三步**。如果有效，请继续执行步骤 4 直到结束。**如果**失败，消息`mkdir: /AppleInternal: Operation not permitted`，**转到步骤 2** 。

### 2.禁用 SIP

SIP 是[系统完整性保护](https://support.apple.com/en-us/HT204899)在 El Capitan 引入的。它保护你免受病毒侵害，在许多情况下甚至保护你自己😜。我们需要禁用它，因为`/AppleInternal`是受保护的区域之一。

*   重启电脑，按住`⌘ CMD + R`直到你的 Mac 重启到恢复模式
*   在顶部菜单中选择`Utilities > Terminal`
*   运行`csrutil disable`
*   使用菜单重新启动它

### 3.创建`/AppleInternal`

你现在应该可以创建`/AppleInternal`了。

*   打开终端应用程序
*   执行`sudo mkdir /AppleInternal`

### 4.享受音乐🎉

通过 Xcode 9 ( `Xcode > Open Developer Tool > Simulator`)打开模拟器应用程序。在菜单中，您现在应该会看到一个新项目，`Internal`。

[![Simulator internal settings](../Images/b2fe91fafe474c8c210ac947d69e987b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vhcJhIwh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blg.zdnkt.com/content/images/2018/08/Screenshot-2018-08-02-at-22.40.32.png)

尽管去玩吧。不要忘记启用全屏模式(`Internal > Allow Fullscreen Mode`)。您在 Xcode 9 Simulator 中所做的更改也应该反映在 Xcode 10 Simulator 中，即使它不显示`Internal`菜单。

### 5.启用 SIP

当你完成你的修改后，重复第 2 步，但是为了安全起见，不要运行`csrutil disable`，**运行`csrutil enable`** 。

我希望你成功并享受全屏模式！

请随时通过 [Twitter @zdnkt](https://twitter.com/zdnkt) 联系我，获取评论、反馈、想法和讨论。

**这篇文章也发表在[我的博客](https://blg.zdnkt.com/fullscreen-xcode-and-simulator/)上。**