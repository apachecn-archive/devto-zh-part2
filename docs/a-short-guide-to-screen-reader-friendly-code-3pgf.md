# 屏幕阅读器友好代码的简短指南

> 原文：<https://dev.to/benrobertson/a-short-guide-to-screen-reader-friendly-code-3pgf>

如果你想有一个可访问的网站，你需要确保它能与屏幕阅读器一起工作。

你会问，什么是屏幕阅读器？**屏幕阅读器**是一款软件，它可以向人大声朗读电脑屏幕上的内容，并让这个人通过键盘与内容进行交互。

## 为什么有关系？

为什么网站必须使用屏幕阅读器？屏幕阅读器软件让有视觉障碍的人能够访问互联网上丰富的信息。如果你想按照预想的方式建设互联网，那么你希望你的网站能够被尽可能多的人访问。毕竟，网页设计[不仅仅是视觉设计](https://benrobertson.io/accessibility/principles-getting-started-website-accessibility#principle-1-web-design-is-more-than-graphic-design)。

幸运的是，HTML 在默认情况下是可以被屏幕阅读器访问的！不幸的是，编写好的 HTML 经常因为许多原因而半途而废。大多数人不知道更好。我知道*我*当我开始做一名网页开发者时，我什么都不知道。

当我们开发人员编写糟糕的 HTML ( [和 JavaScript](https://benrobertson.io/accessibility/javascript-accessibility) 以及 CSS)时，它会使一个网站变得难以理解、不可用，对需要屏幕阅读器的人来说毫无价值。

<small>*想升级你的无障碍游戏？查看我的免费电子邮件课程:✉️ [常见的易访问性错误以及如何避免它们](https://benrobertson.io/courses/common-accessibility-mistakes/)。*</small>

因为 HTML 在默认情况下是可以被屏幕阅读器访问的，所以使用语义 HTML 将会使任何网站对使用屏幕阅读器的人来说都是可用的。为了确保使用屏幕阅读器的用户体验良好，请遵循以下五个提示:

1.  注意标题标签
2.  确保你的网站是导航友好的
3.  每个图像都需要一个 alt 属性
4.  角色属性和里程碑
5.  避免点击`<div>`元素上的事件监听器

## 1。注意标题标签

title 标签是屏幕阅读器用户到达一个页面时听到的第一句话。它帮助人们了解页面的内容，以及他们是否在正确的位置。当他们稍后回到浏览器选项卡时，他们将知道他们在哪个页面上。

这似乎是显而易见的，但是标题标签可能会在大型项目的混乱中丢失。我看到的一个常见错误是设置一个在每个页面上使用的默认标题标签。这个过程导致每个页面都有相同的标题。

假设一个用户试图比较几辆汽车来决定买哪一辆。他们可能会打开 15 个选项卡，每个选项卡都包含评论、品牌和型号详细信息、装饰级别和定价信息。想象一下，如果他们都有相同的标题，他们怎么知道打开哪一个？特别是当你仅仅依靠屏幕阅读器来告诉你一个标签的内容时，这可能会很烦人，所以要确保所有的页面都有一个信息丰富且独特的标题！

## 2。确保你的网站是导航友好的

另一个重要的考虑是，屏幕阅读器为用户提供了不同的导航网站的方式。使用屏幕阅读器的人可能甚至不会使用你精心构建的巨型菜单。事实上，如果不可访问，他们可能**将无法**。让我们来看看他们浏览网站的几种方式:

### 标题

一种选择是标题列表。标题标签构成了网页的轮廓，因此它们是屏幕阅读器使用的自然导航方法。如果你正确使用了标题标签，并且遵循了正确的嵌套顺序(H2 只在 H1 下面，H3 只在 H2 下面，等等)，那么你的网站的轮廓将会是页面内容的清晰概述。这就是为什么**使用标题标签作为结构元素，而不仅仅是样式元素**很重要的原因。

### 列出所有链接

另一个选项是链接列表。屏幕阅读器用户可以获得页面上所有链接的列表，然后浏览它们，跳过所有不是链接的内容。这种导航过程是消除“阅读更多”或“点击这里”等链接文本的一个主要原因。如果有 50 个链接写着“阅读更多”，通过链接菜单导航的屏幕阅读器用户会跳过所有的链接，因为它们没有上下文，基本上没有意义。

确保链接文本准确反映链接内容，以便人们可以找到他们正在寻找的内容，或者[使用 aria 标签为屏幕阅读器提供更多上下文链接内容](https://benrobertson.io/accessibility/principles-getting-started-website-accessibility#aria-attributes)。

### 地标

也许最简洁的导航选项之一是地标菜单。屏幕阅读器会将`<header>`、`<footer>`、`<main>`、`<aside>`、`<nav>`等元素解释为地标元素，让用户直接导航到这些元素。您可以在下面的 macOS VoiceOver 中看到这些地标是如何显示和朗读给用户的。

[![An example of the landmarks menu in macOS Voiceover](../Images/efeea97eed16965e578e94bb9f5c1342.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qJdv9j-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://benrobertson.io/assets/img/landmarks.png)

有意识地使用地标可以给网站带来额外的组织，帮助人们更容易地找到信息。除了使网站更容易访问，决定在网站上使用地标还可以明确网站上不同元素的真正目的。

## 3。每个图像都需要一个 alt 属性

如果图像缺少 alt(可选文本)属性，屏幕阅读器将读取图像文件名。这对于像 201018acn300x450.jpeg 这样由计算机生成的名字来说是非常烦人的。

如果 alt 属性存在并且其中包含文本，则文本将被读取。不需要包括“的照片”或“的图像”，屏幕阅读器将指示它是一个图像。

如果 alt 属性存在并且为空，屏幕阅读器将完全跳过此图像。如果图像不是内容，纯粹是装饰性的，对理解内容或功能不重要，空的 alt 属性可能是一个好策略。

## 4。角色属性和里程碑

如前所述，屏幕阅读器允许人们通过地标导航。这是一个地标菜单的例子，它结合了[地标元素、角色属性](https://benrobertson.io/accessibility/understanding-layout-for-screen-readers#aria-roles-and-landmarks)和 [aria-labels](https://benrobertson.io/accessibility/principles-getting-started-website-accessibility#aria-attributes) 来创建一个干净、易于理解的地标菜单。

[![An example of the landmarks menu in macOS Voiceover](../Images/efeea97eed16965e578e94bb9f5c1342.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qJdv9j-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://benrobertson.io/assets/img/landmarks.png)

以一个`<nav>`元素为例，它将用于定义导航元素。默认情况下，屏幕阅读器会提示用户这是一个导航元素。这可以更进一步，提供一个 aria 标签(`<nav aria-label=”Primary Menu”>`)，告诉用户这是主菜单导航。

在上面的例子截图中，页面的侧边栏、相关帖子、社交分享链接和搜索部分都有标签，使它们更容易被发现。它使整个网站更容易理解和导航。

## 5。避免点击`<div>`元素上的事件监听器

交互式网站的一个常见模式是当用户点击一个元素时，让网站做一些事情。点击这个文本或按钮，菜单就会展开。这个功能是用 Javascript 通过监听点击事件来实现的，就像这样:

```
const interactiveElement = document.querySelector('div.className');
interactiveElement.addEventListener('click', doSomething()); 
```

对于屏幕阅读器来说，监听 HTML div 元素上的点击是有害的，因为 div 没有向屏幕阅读器传递语义信息。

### 下面是问题:

*   VoiceOver 和其他屏幕阅读器将它们显示为通用文本元素。
*   用户将无法知道他们可以点击该元素。
*   如果用户使用键盘浏览页面，他们将永远不会停留在`<div>`上，因为它是不可聚焦的。div 永远不会获得焦点，浏览器会跳过它。
*   此外，voiceover 中还有另一个菜单，显示页面上的所有互动按钮。带有 click 事件的 div 不会出现在这个按钮菜单中。

简而言之，屏幕阅读器无法访问。

让 div 更容易访问是有变通办法的，但是最快的方法是只使用 HTML `<button>`元素。当`<button>`元素监听点击事件时，您可以获得屏幕阅读器和键盘用户使用该元素所需的所有功能:屏幕阅读器会告诉用户这是一个按钮，用户可以直接导航到该按钮，该按钮将与鼠标或键盘具有相同的功能。`<button>`元素做了很多使交互元素可访问的繁重工作，所以使用它们。

(需要帮助设计您的按钮吗？安迪·贝尔的按钮伙伴是一个很好的 CSS 资源！)

## 不会太吓人吧？

我希望你从这 5 个建议中看到，从可访问性开始并不可怕。在很大程度上，*易访问性就是用正确的方式做简单的事情*。无论如何，我们希望让每个人都能更容易地使用和理解网络。

当你追根究底，这是网络可访问性的核心，也是网络的驱动原则:确保任何人都可以访问、使用和理解信息，不管他们使用什么方法与互联网互动。

想要更深入地构建无障碍网站吗？加入我的免费电子邮件课程:📨 *[常见的易访问性错误以及如何避免](https://benrobertson.io/courses/common-accessibility-mistakes/)。30 天，10 节课，100%好玩！*😀 [*在这里报名*](https://benrobertson.io/courses/common-accessibility-mistakes/) ！