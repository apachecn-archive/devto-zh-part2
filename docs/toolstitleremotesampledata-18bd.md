# 工具:title="RemoteSampleData "

> 原文：<https://dev.to/vladimirwrites/toolstitleremotesampledata-18bd>

我从我以前的[文章](https://dev.to/vladimirwrites/toolstitlelayoutpreview-5ae9)中学到的是，获得某个主题的新知识的最好方法是写关于它的文章。当我第一次写这篇文章时，我不知道您可以为您的 Android 项目创建定制的 sampledata。(如果你不知道`sampledata`是什么，请阅读[工具:title="LayoutPreview"](https://dev.to/vladimirwrites/toolstitlelayoutpreview-5ae9) )然后有人给我发了[这个](https://blog.stylingandroid.com/tool-time-part-2/)。

这听起来像是一个非常酷的功能，所以我列出了我最喜欢的指环王中的角色，将其用作`sampledata`，并获得了这个辉煌的布局预览。

[![LotR](img/3ec5e3451a1cf028bf5575eaa3a3cd3a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4Emhsygh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AcZHVJzVAvIfQMI47DEZo1A.png)

后来，我想将它包含在我的其他项目中，但是我唯一的选择是将`sampledata`从一个项目复制/粘贴到另一个项目，这没有任何意义。然后我做了每个(懒惰的)开发者都会做的事情，在 StackOverflow 上问了一个[问题。](https://stackoverflow.com/questions/48874434/use-sample-data-directory-from-a-library)

在浏览了 10 次却没有答案之后，我决定牺牲一点我的名誉，开始一项赏金。而且成功了。我得到了我的答案，我将与你分享。

要在多个项目之间使用`sampledata`,你首先要托管它，然后有一个 Gradle 脚本将它下载到你的项目的`sampledata`文件夹中。这是这样的代码: