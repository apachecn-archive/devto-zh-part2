# 使用 NativeScript 主题生成器赋予您的应用一些风格

> 原文：<https://dev.to/progress/give-your-apps-some-style-with-the-nativescript-theme-builder-1n01>

我不想骗你们，我不是设计师。虽然充其量我可能有一双善于使用白色空间的眼睛——选择互补色和完善 UI 元素的布局远非我所愿。我们知道，一般来说，应用程序开发人员更多的是工程师，而不是艺术家。

虽然我们中最优秀的人拥有计算机科学和美术的双专业学位，但我们中的大多数人依赖其他人(即训练有素的设计师)来提供我们所需要的东西:华丽的设计，其配色方案看起来不像 1996 年的微软 Word 主题。

**进入 [NativeScript 主题生成器](http://www.nativescriptthemebuilder.com/)。**Theme Builder 是一款纯粹的可视化工具，可以非常轻松地将您的原生 UI 元素从极其乏味变为华丽迷人:

[![native ui using theme builder](../Images/4b9cc821924511e5040d09c682ad4068.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mrE1uyWq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/theme-builder/comparison.png)

## {N} + CSS =🤩

用 JavaScript 和 NativeScript 框架编写的移动应用程序是完全原生的，在 iOS 和 Android 平台上都能很好地工作。我们都知道这一点。设计应用程序的方式是使用我们在网络上已经知道并(基本上)喜欢了很多年的[相同的 CSS](https://docs.nativescript.org/ui/styling) 。

和 NativeScript UI 元素都是完全原生的。当我们实例化一个`<Button>`时，它会呈现一个真正的本地`UIButton`(在 iOS 上)和`android.widget.Button`(在 Android 上)。

[![native ios and android buttons](../Images/ac866d5591fe7cccd585274617206918.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EGp6Sc26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/theme-builder/native-buttons.png)

但是本土风格会变得很无聊，而且很快。利用 NativeScript 和 CSS 的强大功能，NativeScript 主题构建器可以为您的原生 UI 元素生成完全兼容的样式。

诀窍是，主题生成器生成的 CSS 并不“只是工作”。**你还需要使用作为核心 NativeScript 框架一部分的[核心主题](https://docs.nativescript.org/ui/theme):**

[![nativescript core themes](../Images/4d227d2cccad427eabed09664f2d928a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DVbaM3YZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/theme-builder/color-schemes-light-and-dark.png)

> 核心主题还附带了多种多样的配色方案，你可以开箱即用。

让我们快速看一下你是如何做到这一点的。

## 使用 NativeScript 主题生成器

要利用由 NativeScript 主题生成器创建的主题，您需要遵循几个简单的步骤:

### 1)使用 NativeScript 核心主题

从 NativeScript 2.4 开始，[核心主题](https://docs.nativescript.org/ui/theme)已经作为所有 NativeScript 项目的一部分发布。如果你的应用由于某种原因是在 2.4 之前的 NativeScript 版本上，你仍然可以用:`npm install nativescript-theme-core --save`安装核心主题。

打开您的`app.css`文件，并确保包含这一行(默认情况下):

```
@import 'nativescript-theme-core/css/core.light.css'; 
```

Enter fullscreen mode Exit fullscreen mode

### 2)使用 NativeScript 主题生成器创建一个样式

这很容易理解，但是导航到 [NativeScript 主题构建器](http://www.nativescriptthemebuilder.com/)并定制你想要的所有颜色、字体大小、边框半径等。一旦你完成了，一定要点击**下载主题**按钮来获得你生成的 CSS 文件。

[![nativescript theme builder](../Images/1173989da67803491e8a55b654fdc00a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--De-8n29d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/theme-builder/theme-builder.png)

有了下载的 CSS 文件，再次打开你的`app.css`文件，使用另一个`@import`语句在核心主题之后引用你的主题*:*

```
@import 'nativescript-theme-core/css/core.light.css';   
@import '<your css directory>/custom.css'; 
```

Enter fullscreen mode Exit fullscreen mode

### 3)在需要的地方添加类

默认情况下，核心主题使用[类选择器](https://docs.nativescript.org/ui/styling#supported-selectors)，而不是元素选择器。这意味着您想要设计样式的每个 UI 小部件都需要应用 CSS 类。幸运的是，这非常简单。

例如，如果您想将一个类应用到一个`<SegmentedBar>`元素，您只需使用下面的 CSS 类:

```
<SegmentedBar class="segmented-bar"> 
```

Enter fullscreen mode Exit fullscreen mode

对于`<Button>`和几乎所有其他原生 UI 元素也是如此:

```
<Button class="btn btn-primary" text="I use the theme's primary color pattern!"></Button> 
```

Enter fullscreen mode Exit fullscreen mode

> 所需 CSS 类的完整列表可以在 NativeScript 文档中找到

一旦你所有的课程都被申请了，你就可以狂欢了！

## 下一步

今天就试试 [NativeScript 主题生成器](http://www.nativescriptthemebuilder.com/)！我敢说，它是免费的(永远都是)，易于使用...好玩！？