# 靛蓝工作室第一印象。将 UX 引入您的应用程序的最佳方式！！

> 原文：<https://dev.to/kenakamu/first-impression-of-indigo-studio-the-best-way-to-bring-ux-to-your-application--94e>

嗨，开发者们！我们都同意 UX 很重要，对吧！？但问题是，你如何采取 UX 的方法来改进你的应用程序。我有机会参加培训并亲自操作 Indigo Studio，这是由 Infragistics 发布的 UX 工具集。

[链接到 Indigo Studio](https://www.infragistics.com/products/indigo-studio)

# 靛蓝工作室客户端

起初，我认为它只是另一个原型桌面应用程序，有 50%是正确的。Indigo Studio 由客户端和服务器端组成。跨平台桌面应用程序给了我们所需要的东西。

*   拖放组件并更改属性，以快速创建原型 UI。如果你已经在 Sketch 中有了 UI 设计，你可以毫不费力地导出/导入。
*   为每个组件添加交互，如点击、滑动、双击。
*   将多种状态添加到一个屏幕上，作为交互的结果，显示动画。
*   将导航添加到交互中，以便用户可以轻松导航多个视图。

**创建您的第一个原型**
[https://www.youtube.com/embed/M4sQ07EhEew](https://www.youtube.com/embed/M4sQ07EhEew)
**添加与 Indigo Studio 的动画**
[https://www.youtube.com/embed/2jQvPr-Mw-M](https://www.youtube.com/embed/2jQvPr-Mw-M)
**添加与 Indigo Studio 的交互**
[https://www.youtube.com/embed/TafjRmFj3Qw](https://www.youtube.com/embed/TafjRmFj3Qw)

除了原型功能，它还提供以下功能。

*   使用 Indigo Studio 服务器与其他团队成员共享项目。
*   发布应用程序，以便任何人都可以使用 Indigo Studio Server 来使用它。
*   创建嵌入每个屏幕的故事板，就像一本漫画书，解释用户如何使用应用程序。

**故事板与靛蓝工作室**
[https://www.youtube.com/embed/QM2Lwq6hKCQ](https://www.youtube.com/embed/QM2Lwq6hKCQ)

## 草图作为 UI 起点

根据培训师的说法，Indigo Studio 不是 Sketch 的替代品，而是它与 Sketch 配合得非常好，因为 Sketch 是 UI 设计工具，Indigo Studio 添加了交互、动画等。

# Indigo Studio 服务器

我不是 100%确定“Indigo Studio 服务器”是不是正确的名称，但是在服务器端有很棒的特性。顺便说一下，有两种类型的服务器。由 Infragistics 托管的服务器，如 PaaS，以及内部版本，如果您需要全面和精细的控制。服务器可以执行以下操作。

*   主持共享项目。您可以在自己的工作区或团队工作区下托管它们。
*   托管已发布的应用程序，并为您提供访问该应用程序的 URL。
*   用户可以对发布的应用程序提出反馈。
*   你可以在中心位置或每个屏幕上看到所有的反馈。
*   您可以向发布的应用程序添加可用性测试。
*   用户可以做可用性测试，服务器记录他们的结果。
*   你可以看到可用性测试结果来分析用户如何使用应用程序。

**indigo designed com**
[https://www.youtube.com/embed/Rc6ur66QKuw](https://www.youtube.com/embed/Rc6ur66QKuw)入门

# 可用性测试

我知道这个产品有很多很棒的特性，但是给我印象最深的是“可用性测试”。为什么？对我来说，这是一个改变游戏规则的几点原因。

### 原型的质量

我们可以通过 Indigo Studio 制作的原型应用程序非常接近真实的应用程序。这不仅仅是点击导航到不同的视图。

举个例子，

*   通过平滑过渡轻松添加手势，例如滑动以显示菜单，或者向左滑动列表中的项目以删除它。
*   点击一个按钮来触发动画动作，甚至故意添加几秒钟的“指示器”,让用户感觉他们实际上是在连接到后端服务器。
*   显示 toast 通知，按钮点击动画，弹出，对话框等。这些小行为使得原型应用程序像真实的应用程序一样

这是真正的可用性测试的关键，它的行为和真正的应用程序完全一样，包括计时。

### 容易做测试

当您让最终用户测试应用程序时，有几个挑战。

*   将应用程序分发给测试人员。
*   解释你想让他们测试什么。
*   收集测试结果。
*   给他们一个反馈的工具。

Indigo Studio 服务器解决了所有这些问题。

*   分发:应用程序在线托管，用户可以通过浏览器轻松访问应用程序。
*   解释:Indigo Studio 服务器为您提供了放置测试场景的地方。用户可以同时访问应用程序和场景。
*   结果:Indigo Studio 服务器记录所有用户交互，如果你愿意，甚至可以用摄像头记录他们的脸。它跟踪用户完成场景需要多少步骤，需要多长时间，与你设定的实现场景的最佳方式相比有多精确。
*   分析:所有的结果都可以通过合理的格式方便地获取。

**创建远程可用性研究**
[https://www.youtube.com/embed/W7IxYTijvx0](https://www.youtube.com/embed/W7IxYTijvx0)

**参加可用性研究**
[https://www.youtube.com/embed/dfrxhBW5NFQ](https://www.youtube.com/embed/dfrxhBW5NFQ)

### 为什么我们要尽早做可用性测试

我们都知道可用性测试很重要，但是有两个问题。

*   你什么时候进行测试？
*   你如何将反馈纳入应用程序？

我看到许多项目只有在实现了后端服务并创建了最少的应用程序之后才进行测试。但是，与“单元测试”或“功能测试”不同，我们不需要任何实际应用或后端服务来做“可用性测试”，对吗？是的，只有当应用程序的行为与实际应用程序几乎相同时。

### 如何收集有用的反馈。

如果用户说，很难弄清楚如何使用应用程序，你打算怎么办？这是很好的反馈吗？号码

这就是 Indigo Studio 服务器带给您的更好的想法。

*   您可以看到每个用户采取的所有步骤。
*   您可以看到每个操作花费了多少秒。

如果 80%的用户试图向左滑动项目来删除项目，那么这就是他们删除项目时的预期。如果 95%的用户试图通过从左向右滑动来打开菜单，那么这就是你可能想要实现应用程序的方式。如果 70%的用户花了超过 5 秒的时间在 UI 上找到一个按钮，那么你可能要改变按钮的位置或颜色。

**查看可用性研究结果**
[https://www.youtube.com/embed/00P6DwMbYMM](https://www.youtube.com/embed/00P6DwMbYMM)

# 少了什么

在我看来，这里缺少了几个特征。

*   并非所有操作都可以在 Indigo Studio 客户端中完成。我需要去浏览器做一些操作。
*   对于 C#等平台，将原型转换成实际代码并不容易。尽管 HTML/JavaScript 工作得很好。
*   Indigo Studio 服务器没有多因素身份验证。这可能是一些客户希望使用内部版本的原因之一。我不知道。

# 总结

如果你曾经感受过我感受到的痛点，我推荐你试试这个产品。

*   编写代码来创建原型，您不希望每次经理或客户要求时都要修改代码来解决小问题。
*   不能很好地采纳反馈，因为很难让用户测试，或者反馈是模糊的，或者采纳改变为时已晚。
*   光是展示概念应用就花了太多时间。

# 引用

有很棒的培训视频，这就是为什么我没有在这篇文章中包含任何技术信息:)
[培训视频&支持文档](https://www.infragistics.com/products/indigo-studio/learning-library)

肯恩(男名)