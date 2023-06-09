# 用 Javascript 和 HTML 构建 AR/VR

> 原文：<https://dev.to/orgoldfus/building-arvr-with-javascript-and-html-1ep1>

[![](img/89c2bd47e75e241cecedb1dfe7260059.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YHqX0LCC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AnbkPFtgm8TPEd8zpPm1Giw.jpeg)

#### 使用网络技术构建引人入胜的增强和虚拟现实体验的综合资源列表

> *这篇文章最初出现在[光环实验室博客](https://blog.halolabs.io/)T3 上*

几个月前，我加入了 Halo Labs，帮助构建我们的 AR/VR 设计和原型开发平台。第一次面试后，我接到了一个“带回家”的练习，要搭建一个简单的 VR 场景。第一次搭建 VR app 兴奋的同时，也有点害怕。我来自一个 web 开发背景，以前从未做过 VR 项目。令我惊讶的是，我花了大约三个小时来完成这个练习，而且它看起来还不错(嗯，至少对我来说……)。在过去的六个月里，每当我告诉别人我正在使用网络技术开发一个虚拟现实产品时，我都会得到困惑的目光。第一届年度 WebXR 周将在两周后举行，我认为这是向网络开发者展示虚拟现实+网络技术= ❤.的好时机

这篇文章的主要目标是让 Web 开发人员快速轻松地进入 AR/VR 世界。我采取的方法不是写一个关于特定技术或库的指南，而是建立一个将带你从零到专家的“课程”，这样你将能够建立复杂的 AR/VR 体验。

即使你不打算开发 AR/VR 体验，阅读本指南也能让你一窥 WebXR 世界的现状。我们开始吧。

### 开始旅程—了解虚拟世界

在我们开始之前，让我们排列一下术语:虚拟现实是使用计算机技术来创建一个**模拟的**环境，所以当你在 VR 中时，你正在查看一个与你面前完全不同的现实。另一方面，增强现实(AR)是通过在你的现实生活中添加数字信息而创建的**增强**版本的现实(就像《口袋妖怪 GO》中一样)。术语 XR 通常用于描述两者中的任何一个。

虽然您可以轻松地跳过这一步，直接跳到 WebVR 框架，但花一些时间学习 XR 世界的基础知识将极大地提高您的学习速度和理解能力。

以下资源将帮助您获得一些关于 VR 和 AR 开发的背景知识，以及所需的(非常基础的)数学背景知识:

*   [uda city 的虚拟现实介绍](https://www.udacity.com/course/introduction-to-virtual-reality--ud1012)课程——这个免费课程是一个很好的起点。该课程介绍了目前可用的主要虚拟现实平台，并解释了它们是如何工作的，同时教授一些基本的(但很重要！)VR 术语。
*   [VR/AR 词汇表](https://imm3rsive.com/en/glossary/) —了解这些基本 XR 术语的含义将有助于您更好地理解文章和 XR 框架文档。另一个很好的资源是[VR 词汇表](http://www.vrglossary.org/)网站。我真的很喜欢他们的信息图表部分，因为它帮助我了解了一些 VR 术语和主题。
*   基础 3D 数学——我进入 VR 世界时最怕的科目是数学。我不是一个数学迷，我认为处理 3D 需要一个彻底的数学知识。幸运的是，事实证明我错了。我将在下面展示的框架是相对“高级”的，不需要任何数学背景。根据我的经验，在继续之前，唯一需要知道的重要事情是[左手和右手坐标系的区别](https://msdn.microsoft.com/en-us/library/windows/desktop/bb324490(v=vs.85).aspx)。

### 在网络上渲染 3D 内容

现在我们对 XR 世界有了一些基本的了解，我们可以开始研究 XR web 框架了。XR 开发的主要框架是 [A-Frame](https://aframe.io/) (由 Mozilla 支持)。下一节将深入探讨 A-Frame，但在此之前，重要的是要了解 A-Frame 是如何构建的，以便有效地使用它。让我们开始吧！

2007 年，Mozilla 首次推出 Canvas 3D，它允许在网络上呈现交互式 3D 图形。下一步是公开一个 API，到 2009 年[Khronos Group](https://www.khronos.org/)成立了 WebGL 工作组。该规范的第一版于 2011 年发布。

但是 WebGL 到底是什么？引用 Mozilla 的一句话:

> WebGL 使 web 内容能够使用基于 OpenGL ES 2.0 的 API，在支持 HTML 画布的浏览器中执行 2D 和 3D 渲染，而无需使用插件。WebGL 程序由用 JavaScript 编写的控制代码和在计算机的图形处理单元(GPU)上执行的着色器代码(GLSL)组成

简而言之，WebGL 是一种能够在浏览器中渲染 3D 内容的 API，无需使用插件。

今天，所有主流浏览器都支持 WebGL API，因此我们可以安全地使用它在 web 上呈现 3D 内容。主要问题？编写 WebGL 既辛苦又乏味。看到显示简单的 2D 图形所需的代码量就足以让人气馁。解决办法？使用 Three.js。

> 该项目的目的是创建一个易于使用的，轻量级的，三维图书馆。该库提供、、CSS3D 和 WebGL 渲染器。
> 
> (来源: [Three.js GitHub 页面](https://github.com/mrdoob/three.js/#threejs))

Three.js 是一个“高级”库，它简化了 WebGL 环境的创建。它为您处理底层编程，让您专注于构建场景。

要了解它在多大程度上简化了开发，请看下面的代码示例，其中[在屏幕上呈现了一个动画 3D 立方体](https://codepen.io/orgoldfus/full/vjdxwb/):