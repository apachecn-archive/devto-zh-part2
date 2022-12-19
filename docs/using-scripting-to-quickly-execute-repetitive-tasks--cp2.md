# 使用脚本快速执行重复性任务

> 原文：<https://dev.to/domysee/using-scripting-to-quickly-execute-repetitive-tasks--cp2>

这是来自我的个人博客的交叉帖子。

有时我不得不为一个项目做一个重复的任务，如果手动完成的话会花费几个小时。Shell 命令通常是可行的，但是我总是很难学会它们，而且在学会之后，我很快就忘记了它们，因为我很少需要它们。所以我找到了另一种解决方法。

脚本！尤其是用**交互的 ruby** 。

例如，最近我不得不重命名一堆文件，删除了*。内嵌"*。

我看过的文件是这样的:

> icon . inline . SVG
> logo . inline . SVG
> (还有更多)

它们应该被重命名为

> 图标 SVG〔t0〕logo .. .svg

好吧，我们开始吧。

1.  导航到文件所在的目录，用`irb`启动交互式 ruby shell(如果安装了 ruby 的话)
2.  获取变量中的所有文件名:

```
irb(main):001:0> filenames = Dir.glob("*.svg") 
```

Enter fullscreen mode Exit fullscreen mode

1.  在循环中重命名它们:

```
irb(main):002:0> filenames.each { |f|
irb(main):003:1* File.rename(f, f.sub(".inline", ""))
irb(main):004:1> } 
```

Enter fullscreen mode Exit fullscreen mode

完成了。很简单，对吧？

现在，这是一个简单的用例。查找 shell 命令不会花费太长时间。

但是如果变得更复杂呢？

如果文件需要包含特殊文本怎么办？如果文件大小必须超过特定的限制，该怎么办？如果...

你知道我在说什么。

有了红宝石，我知道该怎么做了。只需查找我不知道的内容，并将所有内容与我作为开发人员所熟悉的结构结合起来。

因为我从来没有广泛地使用过它，所以我在使用 shell 命令/脚本时会遇到更多的问题。我需要查找如何处理子问题(例如，获取文件大小)以及如何将它们与管道、箭头和其他任何存在的东西结合起来。

此外，Ruby 语句通常更容易记忆。继续以获取文件大小为例，这是 bash 的工作方式([在第一个 Google 结果](https://unix.stackexchange.com/a/16644/276610)中接受答案):

```
stat -f%z icon.inline.svg 
```

Enter fullscreen mode Exit fullscreen mode

与红宝石:

```
File.size("icon.inline.svg") 
```

Enter fullscreen mode Exit fullscreen mode

Ruby 看起来是不是简单多了？

所以，如果你和我一样，不知道如何用 shell 完成复杂的任务，那就给 [interactive ruby](https://en.wikipedia.org/wiki/Interactive_Ruby_Shell) 一个机会。会无限加速那些烦人的重复任务。

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。