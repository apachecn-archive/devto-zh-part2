# 使用自定义 CSS 属性的自定义响应行为

> 原文：<https://dev.to/bnevilleoneill/custom-responsive-behaviour-using-custom-css-properties-5089>

#### 如今，自定义 CSS 属性只作为 CSS 变量使用。下面是如何使用自定义 CSS 属性定义自定义响应行为。

[![](img/3a7cffb37808219e5134542e84af89c4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4_zg_lH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvuH0AlKsDuRalaGMcEtOXA.png)

在 2012 年，自定义属性作为“变量”被引入 CSS 变量模块。后来，“变量”被重命名为“自定义属性”，根据 CSS [规范](https://www.w3.org/TR/css-variables-1/#defining-variables)它将支持除变量替换之外的“其他东西”。

这些“其他的事情”似乎直到现在才发生。自 2015 年以来，该模块仍然是一个候选推荐，尽管主要浏览器都支持它。

自定义属性可以像通常的 CSS 属性一样使用。它们由两个前导破折号后跟一个您选择的标识符来定义，并且可以在任何 CSS 块中定义:

```
.some-selector {
  --my-var: value;
} 
```

该值可以是对 CSS 属性有效的任何表达式。

自定义属性参与级联，即它们被继承并可在媒体查询块中使用。要使用- my-var 作为变量，只需在另一个属性的值中引用它:

```
.other-selector {
  background-color: var(--my-var);
} 
```

关于“变量”用法更详尽的描述，请看这里:[https://developer . Mozilla . org/en-US/docs/Web/CSS/Using _ CSS _ variables](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_variables)

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

#### 同“其他事物”的问题

当你在 CSS 中定义新的自定义属性而不在`var()`表达式中使用它时，没有人会听你的。因此不会产生自定义布局或行为。当然，可以从 JavaScript:
中检索给定元素的自定义属性值

```
window.getComputedStyle(element).getPropertyValue("--my-var"); 
```

这很好，但不一定有意义。在 JavaScript 中，您可以直接为元素编写自定义行为，而不是在 CSS 中为给定的选择器定义一个自定义属性，然后使用上面的代码读取该属性，并根据读取的值编写自定义行为。

你可以利用媒体询问来创造回应行为。getPropertyValue 读取的值将取决于浏览器的当前指标。这也可以直接在 JavaScript 中实现，直接在代码中查询浏览器指标，或者使用 matchMedia 查看当前应用的媒体查询。

#### 真正有意义的地方:框架

当您编写自己的代码时，自定义 CSS 属性没有多大帮助。然而，如果你把它们理解为一个提供定制行为的框架的 API，那就完全不同了。这里，行为(框架)的程序员和用户是不同的人。因此，用户不一定想要深入代码来修改行为。

今天的许多框架都是由 CSS 类(如引导或布尔玛)或 HTML 属性(Angular，Vue)控制的。自定义 CSS 属性是指定参数的第三种方式，可能被证明是定义响应行为的非常有效的方式。

在下一章中，自定义 CSS 属性的可能性将在 layJS 框架示例中解释。

#### 带图层的反应式动画用户界面

LayerJS 是一个用简单的 HTML 标记创建动画分层用户界面的框架。它提供了一个通用的概念，通用的 UI 模式(菜单、滑块、折叠、视差、缩放)可以简单地定义为动态的、重叠的层。

它的行为可以由 HTML 属性控制，或者由上面描述的自定义 CSS 属性控制。在接下来的章节中，我们用它来创建一个菜单，它在桌面上从侧面移动，在手机上以全屏方式淡入。

#### 创建侧菜单

在 layerJS 中，一切都发生在一个“阶段”中。舞台是一个视口，它定义了事情应该发生的区域。在我们的例子中，我们希望所有的事情都在浏览器窗口中发生，所以我们将主体定义为一个阶段。

我们希望菜单出现在主要内容之上，所以我们在 stage 内部定义了两个层，一个用于菜单，一个用于内容。

在菜单层中，我们创建了一个包含菜单实际内容的“框架”,在内容层中，我们创建了一个包含页面内容的框架。

我们不想显示菜单，最初我们设置菜单层的默认框架为“！无":

```
<body lj-type="stage">
  <div id="lcontent" lj-type="layer">
    <div id="home" lj-type="frame">
      ... content ...
    </div>
    ... optional: more content frames
  </div>
  <div id="lmenu" lj-type="layer" lj-default-frame="!none">
    <div id="menu" lj-type="frame">
      ... menu items
    </div>
  </div>
</div> 
```

现在我们定义 UI 的行为。这种行为基本上是框架如何适应/定位到舞台中，以及如何制作动画。我们选择在左侧以浏览器全高显示菜单，宽度固定。这是通过将 lj-fit-to 设置为响应高度来实现的。转换由 lj-transition 定义:

```
#menu {
  --lj-fit-to: responsive-height;
  --lj-transition: left;
  width: 200px;
} 
```

#### 使其反应灵敏

为了使这种行为具有响应性，我们简单地用不同的参数为移动屏幕定义 CSS 子句——菜单应该充满整个屏幕，并且应该淡入:

```
@media (max-width: 600px) {
  #menu {
    --lj-fit-to: responsive;
    --lj-transition: fade;
  }
} 
```

你可以在 JSBin 这里找到一个**活生生的例子** [。点击“菜单”打开和关闭它。确保调整输出部分的大小，以查看响应行为。](https://jsfiddle.net/thomnah/d87kjg3r/)

这个例子展示了自定义 CSS 属性是如何作为一种方便的方法来以一种响应的方式控制框架行为的。最大的优点是，该框架不必对响应性在特定情况下应该如何工作做出任何假设。用户很可能会使用一个网格系统，或者至少是一组断点，框架会无缝地集成到这个概念中。

### 结论

layerJS 尤其受益于自定义 CSS 属性。它提供了从嵌套层中构建任意用户界面的概念。它并不直接实现响应特性，而是让用户决定这些层的内容在不同设备上应该是什么样子。

通过自定义 CSS 属性，用户不仅可以定义布局，还可以定义响应方式的行为。关于 layerJS 概念的解释，请看项目主页上的[动画。关于如何用 layerJS 构建 ui 的简单介绍可以在](https://layerjs.org)[这里](https://medium.com/layerjs/getting-started-with-layerjs-17f679452c8d)找到。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子[使用自定义 CSS 属性的自定义响应行为](https://blog.logrocket.com/custom-responsive-behaviour-using-custom-css-properties-14e2ed8a578f/)最先出现在[博客](https://blog.logrocket.com)上。