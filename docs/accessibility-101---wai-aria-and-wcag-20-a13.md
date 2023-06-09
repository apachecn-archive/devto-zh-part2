# WAI-ARIA 和 WCAG 2.0

> 原文：<https://dev.to/jamesrweb/accessibility-101---wai-aria-and-wcag-20-a13>

## 简介

正如在本系列的介绍文章中所讨论的，WAI-ARIA 和 WCAG 是关于可访问性的两个最重要的规范，如果不是最重要的话，因此我们将在整篇文章中详细讨论这两个规范背后的原则和思想。

## 围空

ARIA 是一套工具和指南，我们可以用它在动态应用程序和站点中提供更易访问的体验。这主要是通过 HTML 属性完成的，在必要的情况下，这些属性通过 javascript 进行更新。

Aria 附带了一组简单的规则，如果遵循这些规则，将真正帮助您为所有用户寻求更易访问的体验！

### 规则 1

如果您可以使用具有所需语义和行为的原生元素或属性，而不是重新规划元素并添加 aria 角色、状态或属性来使其可访问，那么就这样做。

### 规则二

除非万不得已，否则不要更改本机语义。

例如:一个开发人员想要构建一个标签标题。

```
<!-- Do not do this: -->
<h2 role="tab">heading tab</h2>     

<!-- Do this: -->
<div role="tab">
    <h2>heading tab</h2>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

### 规则三

所有交互式 ARIA 元素都必须可以通过键盘使用。

如果您创建了一个用户可以点按、轻按、拖移、放下、滑动或滚动的小部件:

用户还必须能够导航小部件，并使用键盘执行等效的操作。所有交互式小部件都必须编写脚本，以响应适用的标准击键或击键组合。

### 规则 4

不要在*可见* **可聚焦**元素上使用`role="presentation"`或`aria-hidden="true"`。

在*可见的* **可聚焦的**元素上使用其中任何一个都会导致一些用户什么都不关注。

```
<!-- Do not do this: -->
<button role="presentation">press me</button>

<!-- Do not do this: -->
<button aria-hidden="true">press me</button>

<!-- You could do this: -->
<button aria-hidden="true" hidden>press me</button> 
```

Enter fullscreen mode Exit fullscreen mode

### 规则五

所有交互元素必须有一个可访问的名称。

```
<!-- Do not do this: -->
<form action="/path/to/action" method="POST">
    ...
    Enter your name: <input type="text" placeholder="Enter your name" />
    ...
</form>

<!-- Do this: -->
<form action="/path/to/action" method="POST">
    ...
    <label>
        Enter your name:
        <input type="text" placeholder="Enter your name" />
    </label>
    ...
</form> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我将添加一个附录规则，它只是 ARIA 介绍文档中的一个注释，但我认为应该在规则列表中占有一席之地:

### 规则六

尽可能避免咏叹调，但如果你必须使用它，没有咏叹调总比糟糕的咏叹调好。

> 在功能上，ARIA 角色、状态和属性类似于辅助技术的 CSS。对于屏幕阅读器用户，ARIA 控制他们的非视觉体验的呈现。不正确的咏叹调歪曲了视觉体验，对相应的非视觉体验有潜在的破坏性影响。”- [WAI-ARIA 创作实践-没有咏叹调比糟糕的咏叹调好](https://www.w3.org/TR/wai-aria-practices-1.1/#no_aria_better_bad_aria)

## WCAG

理解 WCAG 的核心非常非常重要，因为它为世界上许多国家的无障碍立法提供了基础。在本系列的介绍文章中，我链接了一张地图，显示了使用 WCAG 作为法律基础的国家，以及他们使用什么 WCAG 版本作为法律基础。

尽管其核心，WCAG 归结为 4 个主要原则，这些被统称为倾倒原则。

### 可感知的

*   为非文本内容提供文本选项。
*   为多媒体提供字幕和其他选择。
*   创建可以以不同方式呈现的内容，包括通过辅助技术，而不会失去意义。
*   让用户更容易看到和听到内容。

这一部分是不言自明的，但也有一些领域，有些人有时会陷入困境，让我们看看一些简单的事情，我们可以做些什么来帮助用户感知，以及这与上下文的关系。

首先我们来看看图片:

```
 <!-- Do not do this: -->
    <img src="https://my-awesome-site.com/girl.png" />

    <!-- Do this if the image is only for decoration: -->
    <img src="https://my-awesome-site.com/girl.png" alt="" />

    <!-- Do this if the image provides meaning: -->
    <img src="https://my-awesome-site.com/girl.png" alt="a blonde girl in a white dress sitting on green grass under a blue sky" />

    <!-- You can also this if the image provides meaning and you would like to provide the text for sighted users also, this is also a great thing to do: -->
    <figure>
        <img src="https://my-awesome-site.com/girl.png" alt="" />
        <figcaption>
            A blonde girl in a white dress sitting on green grass under a blue sky
        </figcaption>
    </figure> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们可以看看`audio`和`video`，它们应该总是最好带有转录，这可以通过音频描述和网络视频文本轨道等方式提供。这里是[一篇由 WebAIM 撰写的关于不同多媒体文字记录的文章，例如视频和音频](https://webaim.org/techniques/captions)和另一篇关于[网络视频文本轨道](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API)的文章，例如，它允许我们在视频上提供隐藏字幕。

这个原则还包括使用合适的字体大小和颜色对比，这样用户就可以充分阅读。我们将在接下来的设计文章中了解更多类似的主题。

显然，我们只是触及了表面，但希望这能让你对这个原则的目标有一个好的了解。

### 可操作

*   通过键盘使用所有功能。
*   给用户足够的时间阅读和使用内容。
*   不要使用导致癫痫发作的内容。
*   帮助用户导航和查找内容。

不是每个人都用鼠标导航。有些人可能使用键盘或虚拟键盘。其他人可能通过像龙自然说话这样的工具或某种形式的开关来使用他们的声音。

关键是，你永远不知道你的用户在使用哪一个。即使有最好的分析，也很难确切地知道，因此，你的内容应该可以被所有这些甚至更多的人访问。如果你遵循语义的、简单的、深思熟虑的结构，更重要的是，坚持它们，这是很容易的。请记住，这些技术给用户带来的大部分问题来自我们这些开发人员和设计人员，而不是平台或操作系统。有助于实现这一原则本质的一些事情是:

实现跳转链接，这是一个超级简单的改变，将极大地帮助用户浏览你的网站，绕过不必要和不想要的内容区域。

使用有意义的链接，例如:

```
 <!-- Do not do this: -->
    <a href="https://my-awesome-site.com">here</a>

    <!-- Do this: -->
    <a href="https://my-awesome-site.com">my blog where I tell you about me</a> 
```

Enter fullscreen mode Exit fullscreen mode

这可能是一个复杂的例子，但你应该明白其中的大意。

提供正确的文档结构，例如:

```
 <!-- Do not do this: -->
    ...
    <div class="header">
        <h1>My site title</h1>
    </div>
    <div class="content">
        <h4>Some section title</h4>
        <p>Some long paragraph of content.</p>
        <h6>Some subtitle</h6>
        <p>Some long paragraph of content.</p>
    </div>
    ...

    <!-- Do this: -->

    ...
    <header>
        <h1>My site title</h1>
    </header>
    <section>
        <h2>Some section title</h2>
        <p>Some long paragraph of content.</p>
        <h3>Some subtitle</h3>
        <p>Some long paragraph of content.</p>
    </section>
    ... 
```

Enter fullscreen mode Exit fullscreen mode

看看我们如何使用语义标记赋予元素意义，并按顺序提供标题，而不仅仅是因为它们可能看起来或适合您认为它们应该的样子？

这些元素有一定的意义，也有助于用户使用辅助技术更容易地浏览页面。这意味着更少的代码和更好的体验。这可以根据你的喜好进行推断，无论哪种方式，关键是，语义和结构很重要。

### 可以理解

*   让文本易读易懂。
*   让内容以可预测的方式出现和操作。
*   帮助用户避免和纠正错误。

你写的代码是否与用户看到的相匹配，并为非视觉用户提供了正确的语义，例如:

```
 <!-- Do not do this: -->
    <p>Lorem ipsum dolor sit amet, <span class="some-bold-class">consectetur adipiscing</span> elit. Sed iaculis purus vitae felis cursus.</p>

    <!-- Do this: -->
    <p>Lorem ipsum dolor sit amet, <strong>consectetur adipiscing</strong> elit. Sed iaculis purus vitae felis cursus.</p> 
```

Enter fullscreen mode Exit fullscreen mode

> 使用这样的语义标记，我们可以提供我们想要的样式，并为我们向外界发布的内容提供意义。语义标记将是我们在本系列后面的开发文章中涉及的主题。

另一件要考虑的事情是，你在你写的文章中使用行话或俚语吗？请记住，并不是每个访问你网站的人都是以英语为母语的，所以简单的语言是一个核心的可访问性原则，例如:

```
 <!-- Do not do this: -->
    <p>CPP: In the event of a vehicular collision, a company assigned representative will seek to ascertain the extent and cause of damages to property belonging to all parties involved. Once our representative obtains information that allows us to understand the causality, we may or may not assign appropriate monetary compensation. The resulting decision may occasion one of the following options: the claim is not approved and is assigned a rejected status, the status of the claim is ambiguous and will require additional information before further processing can occur, the claim is partially approved and reduced payment is assigned and issued, or claim is fully approved and total claim payment is assigned and issued.</p>

    <!-- Do this: -->
    <p>
        Claims Processing Procedure (CPP): If you have a car accident, our agent will investigate. Findings will determine any claim payment. This could result in:
    </p>
    <ul>
      <li>Approved claim - full payment</li>
      <li>Partially approved claim - reduced payment</li>
      <li>Undetermined claim - more information needed</li>
      <li>Rejected claim - no payment</li>
    </ul> 
```

Enter fullscreen mode Exit fullscreen mode

*以[围写提示页](https://www.w3.org/WAI/tips/writing/)为例。*

我们仍在传达所有需要的信息，但与以前不同的是，它现在更容易被更广泛的用户所消化和理解。

这个原则的另一个考虑因素是图像、视频和音频，所有这些我们之前都讨论过。在这个原则的情况下，它是关于确保你为这些项目正确地标记代码，正如我们已经看到的。与此同时，确保你在 alt、figcaption 或文本轨道中使用的文本内容尽可能简单易懂。

在这个原则中，有许多不同的东西需要考虑，远远超过我所举的例子，但我希望这是一个良好的基础，让你在你的项目和研究中继续考虑这些原则。

### 健壮

*   最大限度地兼容当前和未来的用户工具。

这个原则主要归结为一个问题:我是否在不同的浏览器、设备和操作系统、设备大小等方面提供了相似的外观和行为？

## 结论

这需要吸收大量的信息，但是如果你能够理解、接受并开始使用艾瑞亚和 WCAG 的这些原则，你将会有一个很好的前进基础。

我强烈建议您去查看以下资源，继续学习这些主题:

*   [使用咏叹调](https://www.w3.org/TR/using-aria/)
*   [咏叹调创作实践](https://www.w3.org/TR/wai-aria-practices-1.1/)
*   [WCAG 2.1 规范](https://www.w3.org/TR/WCAG21/)
*   [了解 WCAG 2.1](https://www.w3.org/WAI/WCAG21/Understanding/)
*   [WCAG 2.0 的咏叹调技巧](https://www.w3.org/TR/WCAG20-TECHS/aria)

下一集再见！