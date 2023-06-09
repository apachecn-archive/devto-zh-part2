# Chrome 69 中的新功能:构建带有锥形渐变的<progress>圆环图</progress>

> 原文：<https://dev.to/bnevilleoneill/new-in-chrome-69-building-progress-doughnut-charts-with-conical-gradients-2bjj>

[![](img/f36283a13cead2fe7c2c48f4aa5f832a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WR4qJP3S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AluFSgEXjO7A-BmbWtQTrIA.jpeg)

每个人都喜欢图表。它们以简单的方式传达信息，一眼就能迅速理解。每个人都喜欢甜甜圈。它们是美味可口的甜甜圈。

饼图和甜甜圈图是我们喜欢但很少在 web 上看到的元素之一，主要是因为创建它们涉及的技术复杂性。我们一直依赖于带有 JavaScript 库的 canvas，或者带有 CSS 和 SVGs 的一些非常粗糙(并且很棒)的方法，但是它们仍然很复杂并且缺乏语义。

幸运的是，随着最近 Chrome 69 的发布，我们终于支持关键成分——圆锥形渐变。现在，我们可以用极其简单和语义化的方式创建饼图和甜甜圈图。

### 圆锥形什么？

圆锥形渐变是填充渐变，它们的颜色停止点*在*圆周周围。它们是 [CSS 图像值的一部分，取代了内容级别 4](https://www.w3.org/TR/css-images-4/#conic-gradients) 模块，并且已经工作多年，但是直到最近还没有浏览器实现它们。

在其最基本的应用中，圆锥形渐变看起来像一个方向光效果，并且通过多个光圈，我们可以轻松地创建色轮。

```
.cone{
  background: conic-gradient(orange .1turn, red, orange 326deg);
} 
```

[![](img/29b91643f91af01dde29c2d8276ef5ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Uv037C6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/206/0%2APeToYmmkfl-xR1TG.png) 

<figcaption>平行光效果使用锥形渐变</figcaption>

```
.color-wheel{
  background: conic-gradient(red, yellow, lime, aqua, blue, magenta, red);
} 
```

[![](img/084ab306e992621b4de6cc485dccd58d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iAOPV3Jz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/209/0%2AUl2pgX1EiENRtKrH.png) 

<figcaption>色轮使用锥形渐变</figcaption>

与我们对线性和径向渐变所做的创建交替纯色图案的操作类似，我们可以在相等的位置定义锥形渐变的颜色停止点(或从 0 开始的后续停止点)，有效地将它变成饼图。

```
.linear-background{
  background: linear-gradient(to right, red 25%, yellow 25%, yellow 50%, green 50%, green 75%, blue 75%, blue 100%);
} 
```

[![](img/d1f01025570dc358a7eb0f209e43510a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---otUbhFr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/216/0%2ANcRbPZ2P_3gGbxGp.png) 

<figcaption>实停直线渐变</figcaption>

```
.pie-chart{
  background: conic-gradient(red 33%, green 33%, green 66%, yellow 66%);
} 
```

[![](img/e32089c812ad5c11947478a93d829ba6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UULeb-Uj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/206/0%2AOhhvrQXTFXuKznwr.png) 

<figcaption>简单饼图使用实心圆锥形渐变</figcaption>

从那里到一个甜甜圈图是很容易的，因为应用一个单一的停止圆锥梯度遮罩。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/signup/)

### 右边标注

HTML 5 引入了许多额外的语义元素，其中一些对大多数开发人员来说仍然知之甚少。对于圆环图，我们可以很容易地使用单个

, but there are two elements better suited for it: and .

**进度**表示给定任务的完成进度，例如文件下载。

浏览器在如何实现它上有一些自由(可能太自由了)，但是它通常显示为一个进度条。除了全局属性之外，它们接受 max 来表示任务完成的次数(默认为 1)，接受 value 来表示已经完成了多少(范围从 0 到 max)

**计量器**用于表示已知范围内的标量值，例如磁盘使用量。它的渲染方式类似于 progress，但针对不同的范围添加了其他样式。

属性包括当前值、min 表示最小值(默认为 0)、max 表示最大值(默认为 1)、low、high 和 optimum，以根据给定的阈值给出不同的样式(例如，当磁盘使用率高于 90%时，以红色显示条形)。它也可以通过表单参数与表单元素(如输入)相关联。

考虑到这一点，我们的图表的语义正确的 HTML 标记应该是这样的:

```
/\* 50% filled progress bar \*/
<progress value="50" max="100"></progress>

/\* 50% filled meter bar \*/
<meter value="50" max="100"></meter> 
```

### 取消元素样式

与其他复杂的表单元素类似，浏览器依赖于供应商特定的伪元素来创建或的不同可视组件。它们还使用 appearance 属性根据操作系统的主题来设计元素的样式。所以，第一步是摆脱所有这些:

```
/\* gets rid of custom appearance provided by the operating system's theme \*/

progress, meter{
  -webkit-appearance: none;
  appearance: none;
}

/\* hides all <progress> pseudo elements from webkit \*/

::-webkit-progress-inner-element, ::-webkit-progress-bar, ::-webkit-progress-value{
  display:none;
}

/\* hides all <meter> pseudo elements from webkit \*/

::-webkit-meter-bar, ::-webkit-meter-optimum-value, ::-webkit-meter-suboptimum-value,::-webkit-meter-even-less-good-value {
  display: none;
} 
```

这将为我们留下一个空容器(类似于一个空的 div ),我们可以随意设置它的样式来创建饼图/圆环图。

**我们只需要隐藏手头任务的所有元素**，但这里有一个所有这些工作方式的参考，以防你想设计进度条:

*   外观:none 从 OS 主题中禁用自定义样式，这是对任何内部组件进行样式化所必需的。

**进度伪差:**

*   ::-webkit-progress-inner-element，尽管名称令人困惑，但表示 progress 元素的最外层容器。我们可以在这里添加边框和阴影。
*   ::-webkit-progress-bar 样式化整个进度条，它通常只作为未填充部分可见，因为填充部分呈现在它上面。我们通常会改变背景。
*   ::-webkit-progress-value 是该栏的填充部分，同样，背景是我们通常在其上设置的样式

**计伪:**

*   ::-webkit-meter-bar 样式化整个仪表栏，作为未填充部分可见。填充的部分由其他三个伪元素控制。请注意，根据给定值及其与低、高和最佳参数的关系，在给定时间内，它们中只有一个处于活动状态。
*   ::-webkit-meter-optimum-value 当值在从低到高的范围内时对其进行样式化。
*   ::-WebKit-meter-suboptimum-当值超出低到高范围时应用值。
*   ::-WebKit-meter-even less-good-value 当值在低到高范围之外并且在最佳值的相反侧时应用

### 创建图表

随着所有自定义样式的出现，我们可以定义我们的图表大小，并简单地使用 border-radius 将它变成一个圆形，用单步锥形渐变填充它。

为了定义该步骤的值，我们可以使用一个 CSS 自定义属性(也称为 CSS 变量):

```
progress, meter{
  position: relative;
  width: 100px; height: 100px;
  border-radius: 50%;
  --fill: calc(var(--progress) \* 1%);
  background: conic-gradient(CornflowerBlue var(--fill), LightGrey 0);
} 
```

这将给出一个饼图，第一种颜色(浅蓝色)填充到可变百分比，第二种颜色(浅灰色)填充其余部分。

在理想情况下，我们将使用在 [CSS 值和单位模块级别 3](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/progress) 中定义的 attr()函数来分配来自实际值属性的变量。

```
--progress: attr(value); 
```

不幸的是，除了伪元素的内容之外，在任何其他属性中使用 attr()函数目前都不被任何浏览器支持，所以我们需要通过一点 JavaScript 解析来分配它，或者在元素的内联样式中重复该值。

我选择了后者，只是为了在这个演示中只使用 CSS，但是用 JS 方法来分配 CSS 变量对于可维护性、可重用性和保持简洁来说会更好。

```
<progress class="doughnut" value="50" max="100" style="--progress:50"></progress>

<meter class="doughnut" value="50" max="100" style="--progress:50"></meter> 
```

下一步是将饼图变成甜甜圈。这可以通过在饼图顶部覆盖一个伪元素来轻松实现，用纯色背景作为一种遮罩，用 value 属性作为内容

```
/\* "masks" the pie chart, overlays text \*/
progress::after, meter::after{
  content: attr(value) "%";
  position: absolute;
  top:8px; right:8px; bottom:8px; left:8px; /\* bar thickness \*/
  background: white; /\* "mask" color \*/
  border-radius: 50%;

  /\* centers text \*/
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

这就是我们的 Chrome 69+的单元素、语义、易于编码的甜甜圈图

[https://medium . com/media/0c 7905342 DD 71 e 87 FB 7d 1929 CD 423458/href](https://medium.com/media/0c7905342dd71e87fb7d1929cd423458/href)

### 使用实际掩蔽

现实世界的实现可能会要求内圈是一个实际的遮罩，而不是硬编码的背景色，以便在不同颜色的容器中使用它。浏览器对 mask 属性的支持比锥形渐变好得多，所以这应该很容易。

```
mask:radial-gradient(closest-side, transparent calc(100% - 10px), black calc(100% - 8px)); 
```

前面的代码创建了一个蒙版，它由透明填充组成，一直到最后 9px，以及纯黑色(可以声明为任何颜色，重要的是 alpha 通道为 1)取最后 8px，有效地从元素中“剪切”除了外部 8px(即进度条厚度)之外的所有内容。步骤之间的 1px 差异是为了防止锐边。

这里的问题是，在元素上应用掩码也会去掉伪元素上显示的文本。所以，我们需要把渐变和蒙版分离成各自的伪，用另一个显示文本。

```
/\* element styles only define sizing \*/
.progress, meter{
  position: relative;
  width: 100px; 
  height: 100px;
}

/\* using a conical gradient + masking on a pseudo to create the doughnut chart. \*/
progress::before, meter::before{
  content: "";
  position: absolute;
  top: 0; left: 0; right: 0; bottom: 0;
  border-radius: 50%;
  --fill: calc(var(--progress) \* 1%);
  background: conic-gradient(CornflowerBlue var(--fill), lightgrey 0);
  mask:radial-gradient(closest-side, transparent calc(100% - 9px), black calc(100% - 8px));
}

/\* overlays text using the other pseudo \*/
progress::after, meter::after{
  content: attr(value) "%";
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

这提供了一个更好看、更可重用的圆环图。

[https://medium . com/media/2a 9773 b 1934787 CB 036532 b 83 F8 b 9693/href](https://medium.com/media/2a9773b1934787cb036532b83f8b9693/href)

### 其他浏览器呢？

Chrome 69 是第一个正式支持锥形渐变的浏览器，而 [Webkit 刚刚实现了它](https://bugs.webkit.org/show_bug.cgi?id=189329)，所以我们可以期待它在基于 Webkit 的浏览器的下一个版本中出现。

到目前为止，Edge 和 Firefox 还没有计划支持的迹象。

所以第一件要做的事情是考虑把它作为渐进的增强，用不起眼的进度条作为退路。这可以通过将我们的甜甜圈图声明包装在@supports 查询
中轻松实现

```
[@supports](http://twitter.com/supports)(background: conic-gradient(#000 50%, #FFF 0)){
  /\* (...) \*/
} 
```

也就是说，由于 Lea Verou 的 polyfill，我们可以在其他现代浏览器中使用锥形渐变。

要使用它，我们只需要包括圆锥梯度 polyfill.js 文件，以及它的依赖前缀自由。

```
<script src="[https://cdnjs.cloudflare.com/ajax/libs/prefixfree/1.0.7/prefixfree.min.js](https://cdnjs.cloudflare.com/ajax/libs/prefixfree/1.0.7/prefixfree.min.js)"></script>
<script src="conic-gradient.js"></script> 
```

尽管有一些限制，最明显的是缺乏对 calc()和 var()的支持。所以请记住这一点，因为使用 polyfill 会阻止我们将填充集作为 CSS 变量。我们可以使用一些 JS 来读取每个的值，并直接从 JS 设置背景，而不是设置 CSS 自定义属性。

正如我们对 Chrome 所做的那样，我们应该隐藏 Firefox 和 Edge 的伪元素。幸运的是，对他们来说，这是一个单一的元素。

```
/\* hides Firefox progress fill bar \*/
::-moz-progress-bar {
  visibility: hidden;
}
/\* hides Edge progress fill bar \*/
::-ms-fill {
  display: none;
} 
```

注意我们使用 visibility:hidden 而不是 display:none 来隐藏 Firefox 的进度条，因为后者没有任何效果，即使标记为！重要。我们也不能改变伪元素的宽度或以任何其他方式塑造它。看起来火狐把它设计成了内嵌的！重要，所以我们只能在那里更改一些属性。

但是为什么我们首先要设计 pseudo 的样式呢？事实证明，Firefox 不支持两者中的::before 或::after 伪元素，因此设计::moz 进度条似乎是应用掩码或文本内容的可行方法。但事实并非如此。

不幸的是，我们别无选择，只能改变我们的标记，使其在 Firefox 上工作。

包装一个语义中立的元素(比如 div 或 span)将值显示为文本是我能找到的最简单的方法

**HTML**

```
<span class="doughnut" data-value="40"><progress value="40" max="100"></progress></span> 
```

**CSS**

```
.doughnut{
  position:relative;
}

.doughnut progress{
  -moz-appearance: none;
  appearance: none;
  width: 100px;
  height: 100px;
  display: block;
  border:0;
  border-radius:50%;
  background: conic-gradient(CornflowerBlue 40%, lightgrey 0);
  mask:radial-gradient(closest-side, transparent calc(100% - 9px), black calc(100% - 8px));
}

/\* overlays text on the span's pseudo \*/
.doughnut: after{
  content: attr(data-value) "%";
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
} 
```

这不是世界上最好的事情，特别是因为这需要在 span 上重复数据属性中的值。但至少有用。

[https://medium . com/media/89ae 293 fc 364 CB 801 b 0 BD 5c 814282 D5 b/href](https://medium.com/media/89ae293fc364cb801b0bd5c814282d5b/href)

进入 Edge 领域，主要的限制是 Edge 17 缺乏对 mask 属性的支持，这是目前的版本。它可以通过“启用 CSS 屏蔽”标志来启用，但这显然是我们不能要求用户做的事情。因此，纯色覆盖可能是正确的方法。

好消息是 Edge 18 计划为面具提供支持。

### 包装完毕

圆锥形渐变让我们很容易将枯燥的进度条变成令人惊叹的甜甜圈图。此外，它们可以用于各种应用:饼图、色轮、3D 模拟、闪电效果、星爆。连[视错觉](https://codepen.io/Rplus/pen/eNGbrg/)都可以轻松搞定。

开发者对这个特性的需求并不高，这可能是浏览器花了这么长时间才开始实现的原因。但有了 Chrome 69 和 WebKit 的引领，其他人没有理由不跟随。

如果你也想看到他们实现锥形渐变，请确保在 [Gecko bug 报告](https://bugzilla.mozilla.org/show_bug.cgi?id=1175958)和 [Edge uservoice 线程](https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer/suggestions/8471413-implement-conic-gradients-from-css-image-values-le)上制造一些噪音。我们可以让网络变得更漂亮一点。也更美味。

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *