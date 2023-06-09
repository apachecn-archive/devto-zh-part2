# 如何制作或转换 LTR 网站为 RTL 或从右向左的网站？

> 原文：<https://dev.to/moreonfew/how-to-make-or-convert-ltr-website-to-a-rtl-or-right-to-left-website-4dn7>

我相信我们都希望我们的网站被全世界的人访问和喜爱。是的，我们也希望它能在社交媒体网站上分享，让世界各地的人都能看到它。然而，很多时候我们可能不会想到，我们的游客可能来自遵循 RTL(从右到左)脚本的国家。我说的 RTL 文字是指阿拉伯语(العَرَبِية‎)、乌尔都语( **اُردُو语**)、希伯来语(עברית)、意第绪语(יידיש)等语言的文字，这些语言的读写是从右向左进行的，不像英语和大多数其他语言的读写是从左向右进行的。那么，我们如何使我们的网站对使用 RTL 语言的访问者友好呢？嗯，这就是我今天要告诉你们的，[如何将你的网站从 LTR 转换到 RTL](https://www.moreonfew.com/how-to-convert-ltr-website-to-rtl-website/) 。

## 如何给网站添加 RTL 语言支持？

当你给你的网站添加从右到左或 RTL 语言支持时，要考虑的最重要的事情是为你的网站做一个**镜像布局**。也就是说，所有在左边的元素都会将它们的位置切换到右边。例如，看看下面的图片，其中有雅虎美国版(LTR)和雅虎中东(RTL)版的屏幕截图:

[![Yahoo's LTR and RTL version](img/af48148243e856b249db9d8bfb54d495.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RvocRdOJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.moreonfew.com/wp-content/uploads/2015/11/Mirror_Layout_website.jpg%3Fresize%3D700%252C600%26ssl%3D1) 
*雅虎的 LTR 和 RTL 版*

在上图中，你可以注意到页面上的所有元素或组件都交换了位置。原本在页面左侧的徽标现在出现在页面的右侧。搜索框和按钮也改变了它们的位置，轮播标题也是如此。注意侧菜单，它也向右移动了。

好吧，对于习惯了 LTR 阅读的读者来说，这可能看起来有点奇怪，但对于习惯了 RTL 的读者来说，这实际上是非常正常和“正确”的。因此，很重要的一点是要警惕组件应该如何放置，例如:按钮在文本字段之前，标签在文本框之后(对于习惯于 LTR 语言的人来说，这就是它的样子)。

## 那么，我该怎么把我的站点从 LTR 转换成 RTL 或者从右向左呢？

你可以[将你的网站从从左到右转换成从右到左](https://www.moreonfew.com/how-to-convert-ltr-website-to-rtl-website/)比你想象的要容易得多。

### 使用方向 rtl 属性将 html 转换为 RTL

**首先是**，你需要将所有 LTR 元素的方向改为 RTL。您可以通过使用 HTML 的`dir`属性来做到这一点。您可以编辑`<html>`标签来包含`dir="rtl"`。因此您的标签应该类似于:

```
<html dir="rtl"> 
```

Enter fullscreen mode Exit fullscreen mode

### 从右到左 CSS 注意事项

**其次**，你需要改变方向敏感的 CSS 属性来适应 RTL 设计流程。换句话说，像边距、填充、浮动、背景位置、左边界等方向敏感的 CSS 属性的值需要被编辑。比如说我在 CSS 中有一个类似
的属性

```
 margin: 0 0 10px 20px; /* (i.e margin:top right bottom left) */ 
```

Enter fullscreen mode Exit fullscreen mode

这需要改成

```
margin:0 20px 10px 0; 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意到，我们用左边距值交换了右边距值。所有方向敏感的 CSS 属性都需要这样做。以下是所有方向敏感 CSS 属性的列表:

| 方向敏感 CSS 属性 |
| --- |
| `background` |
| `background-image` |
| `background-position` |
| `background-position-x` |
| `border-bottom-left-radius` |
| `border-bottom-right-radius` |
| `border-color` |
| `border-left` |
| `border-left-color` |
| `border-left-style` |
| `border-left-width` |
| `border-radius` |
| `border-right` |

第三，现在你的页面已经有 90%是 RTL 了！但是，您可能仍然会注意到一些组件被破坏，这可能需要您根据第一步和第二步来检查和编辑 CSS 值。

### 自动将 CSS 从 LTR 转换为 RTL 的工具

虽然您可以手动转换 CSS 以满足 RTL 要求，但也有许多工具可供您使用。我列出了其中的几个:

*   [CSS FLIP](https://github.com/twitter/css-flip) : CSS FLIP 是 Twitter 的一个项目，帮助你将你的 LTR CSS 转换成 RTL。您可以使用`npm install css-flip`通过 npm 安装它。要生成 rtl css，您可以使用他们的 CLI 并以`css-flip path/to/file.css > path/to/file.rtl.css`的身份运行命令
*   [RTL CSS](https://github.com/MohammadYounes/rtlcss) : RTL CSS 也提供类似 CSS 翻转的功能。可以用 npm 代码`npm install -g rtlcss`安装。您也可以将它与 PostCSS 等一起使用，或者您也可以使用它们的 CLI 并运行命令`rtlcss input.css output.rtl.css`。
*   CSSJanus 是一个在线工具，你可以通过复制粘贴你的 ltr CSS 来转换成 RTL CSS。如果你不想经历安装包的麻烦或者如果你不想使用节点包，这是一个很好的工具。
*   Rastchin : Rastchin 是另一个在线 LTR 到 RTL CSS 转换器，它类似于 CSSJanus，但也提供了更多的选项，如生成缩小的 RTL CSS 输出，林挺的 LTR CSS 等。

虽然有许多工具可以将 LTR CSS 转换为 RTL，但我建议您首先了解需要采取哪些步骤来将您的 LTR 网站或 CSS 转换为 RTL 以及最好的方法。它可以是手动的，使用工具或者两者的混合，因为我相信即使使用了工具，结果也可能不是 100%精确到你想要的，因此你可能需要手动编辑一些组件。无论如何，让我们知道你把你的 LTR 网站转换到 RTL 的经历。

如何制作或转换 LTR 网站为 RTL 或从右向左的网站？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。