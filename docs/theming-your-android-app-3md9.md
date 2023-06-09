# 为您的 Android 应用程序设置主题

> 原文：<https://dev.to/pabiforbes/theming-your-android-app-3md9>

如果你对设计和颜色组合没有眼光，给你的 android 应用主题化可能是一项相当艰巨的任务。当你被安排在一个由 UI/UX 设计师设计的项目中时，事情就简单多了。主题应用于整个应用程序，而不仅仅是单个视图。应用程序主题化包括为其创建配色方案。一个 android 应用的配色方案是由一种原色组成的，或者有时是原色和二次色的组合。

**原色**
原色是用户在屏幕和应用程序组件上最常看到的颜色。例如，原色会出现在应用程序栏、状态栏和标签视图中。

**二级色**
二级色可以用来强调屏幕上的不同组件。例如，第二种颜色可以用在浮动操作按钮、进度条或开关上。

*旁注:*
并不总是要求有二次色。该应用程序可以使用原色的变体作为它的第二颜色。原色变体是颜色的较亮和较暗的阴影。

那么，我们如何解决应用程序主题化的任务呢？幸运的是对于 android 初学者来说，[材质设计](https://material.io/)有一个很酷的调色板工具，叫做[颜色工具](https://material.io/tools/color/#!/?view.left=0&view.right=0)。

[![Visible Textview](img/c548e85b1e81261c6378062c15eb9ad1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cMdd9c2_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/waozfo1n7mx5wodsn82l.PNG)

颜色工具有助于您可视化应用程序在您选择的颜色下的外观。该工具还允许您导出颜色 xml，并将其粘贴到 android colors.xml 文件中。
您只需点击网站右上角的*导出*按钮，它就会以 xml 文件的形式下载您的方案。然后，您只需将 xml 文件中的文本复制到 Android 项目的 color.xml 文件中，如下所示:
[![Visible Textview](img/da2afc8de122296729de2db5602376fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Izasxh0w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/epl7rz9uowffxo20i43a.PNG)

另一个很棒的工具是[材质设计面板](https://www.materialpalette.com/)，它也提供了主题的可视化。您可以简单地单击下载按钮来获取该方案的 xml 版本，然后简单地将其复制并粘贴到 colors.xml 文件中。
T3![Visible Textview](img/d668aebb989a162c793eaa60250e7086.png)T5】

有了这些可用的工具，现在设计你的应用就更容易了。一些有用的资源:
- [素材设计](https://material.io/)
- [安卓开发者指南](https://developer.android.com/guide/topics/ui/look-and-feel/themes)

需要一些灵感吗？看看其他开发者和设计师在 [UpLabs](https://www.uplabs.com/android) 上制作了什么。

*演职员表:*
封面图片——非常感谢马库斯·斯皮斯克，摘自[昂斯普什](https://unsplash.com/)