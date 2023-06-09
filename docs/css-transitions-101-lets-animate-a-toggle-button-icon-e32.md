# CSS 转换 101:让我们动画一个切换按钮图标

> 原文：<https://dev.to/bnevilleoneill/css-transitions-101-lets-animate-a-toggle-button-icon-e32>

### CSS 转换 101:让我们制作一个切换按钮图标的动画

[![](img/e9a088f47434d3294efe6f66ed1d2d92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_8Kdreue--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AScwkb2huLr7PUNS8rEMKqQ.jpeg)

网络动画的作用远远不止是一件装饰品。你可以用它来引导网站访问者的注意力，组织信息并使其更容易消化，让等待内容加载感觉更快更有趣，等等。

好消息是，有了前端开发人员工具包中的 CSS 转换，您将能够在短短一行代码中添加一些特色，并改善用户在 web 上的体验。

在这里，您将了解 CSS 转换何时对您的项目是一个好的选择，以及如何实现它们来美化您的网站。在本文结束时，您将已经使用 CSS 转换在切换按钮上创建了变形动画。

我们开始吧！

### CSS 转场或者关键帧动画？

你可以单独用 CSS 添加平滑的运动效果，不需要 JavaScript，无论是用 [CSS 过渡](https://www.w3.org/TR/css-transitions-1/)还是 [CSS 关键帧动画](https://www.w3.org/TR/css-animations-1/)。就性能而言，它们都很高效，尤其是当你制作不透明度动画和[变换](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)属性时，这是浏览器非常擅长优化的。

**跃迁**

转换允许您将属性值从起始状态更改为结束状态，以响应事件，例如，鼠标输入、鼠标输出、单击等。这意味着，如果你的动画只有这两种状态，CSS 转换将是你可以使用的最好和最简单的工具。过渡的常见用例包括在悬停或鼠标点击时滑入和滑出画布外的侧边栏，在悬停时改变链接或按钮的颜色，响应于按钮点击而淡入或淡出对话框，等等。

使用过渡的另一个好处是**优雅的降级**:如果出现错误或者浏览器不支持它们，最坏的情况是元素会突然改变状态，而不是逐渐改变。

**动画**

如果你计划创建的动画不仅仅有起始状态和结束状态，并且你想对所有中间状态的发生有更多的控制，那么 CSS 关键帧将更适合你的项目。

关键帧动画的用例包括加载器，它在页面加载后立即开始播放，并无限期地播放，直到所请求的页面资源准备好显示在屏幕上。

[![](img/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 过渡属性

你用 CSS **转场**属性:
创建转场

```
.selector {
  transition: property duration transition-timing-function delay;
} 
```

上面的代码是这四个属性的简写版本:

```
.selector {
  transition-property: color;
  transition-duration: 0.5s;
  transition-timing-function: ease-in;
  transition-delay: 0.3s;
} 
```

**过渡属性**

transition-property 的值是要应用过渡的 CSS 属性。这可以是任何 CSS 动画属性，如颜色、高度、宽度等。

并不是所有的 CSS 属性都可以转换，但是很多都可以，特别是那些用数值表示的属性。这些可以具有开始状态和结束状态，它们可以容易地用表示相应中间状态的中间数字来内插。您不能转换值**自动**。

如果您计划转换多个属性，我认为使用关键字 all 会更好，它会将转换应用到所有用于样式化所选元素的动画属性。

**过渡持续时间**

transition-duration 属性表示将 CSS 属性从起始值转换到结束值所需的时间。您可以用秒(1s)或毫秒(1000ms)来表示值。

**过渡-计时-功能**

如果你想让你的过渡感觉自然和流畅，过渡-定时-功能属性是至关重要的。它有一个三次贝塞尔曲线作为值来表示动画中的**变化率**。为了简单起见，您可以使用以下关键字:

*   **缓动**:默认值。它开始缓慢，然后加速，然后减速，最后非常缓慢地结束:

[![](img/8bc3c6d6b8dce00d4a0ab52f2677d8b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FQtgdUzQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/444/1%2ANpJ89CwvjF5RFFIJnIRqhg.gif)

*   **线性**:变化率保持不变；

[![](img/953ad9acde3d535560cd364c7c4d4379.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hjznnhyn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/445/1%2Ar364uArJmTILNMFeANRPYg.gif)

*   **缓和**:开始缓慢，然后加快速度:

[![](img/43449a8cc242527d3262615aa11a1efd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--llk-du5m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/443/1%2ARJA-qAGlh7F_mzvScTppfg.gif)

*   **放松**:快速启动，然后减速:

[![](img/a532f04defc7926b6f8ab003add7b4e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Lk5w3ue8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/440/1%2Avp_ihURQgxdNN_pdr9N6jw.gif)

*   **渐入渐出**:开始慢，中间快，到最后慢下来。它类似于缓和，但结尾没有那么慢:

[![](img/17784f8ba820b537c6d6dde05b876537.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9tgfwGoq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/439/1%2A0x2Eejg8N1otFO_qqxtLEw.gif)

或者，你可以创建自定义的三次贝塞尔曲线，你可以使用像[cubic-bezier.com](http://cubic-bezier.com/#.17,.67,.83,.67)这样的工具快速构建。

**过渡延迟**

transition-delay 属性表示在开始持续时间之前要等待的时间。就像 duration 属性一样，可以用秒或毫秒来表示这个值。

在本教程中，您将使用速记属性。

请记住，持续时间是唯一必需的值，所有其他属性都有默认值。过渡属性默认为 all，过渡计时功能默认为 ease，过渡延迟默认为 0s。如果您为持续时间和延迟都添加了两个值，浏览器会将第一个值解释为过渡持续时间，将第二个值解释为过渡延迟，因此添加这些值的顺序非常重要。

### 渐变切换按钮图标

现在是时候看看一些 CSS 转换的效果了，让我们进入编码模式吧！

目标是将切换按钮上的图标从汉堡形状变成 X 形状。结果将会是这样的:

[![](img/c00106d2d2646c2ba7e62bd5a0faf92c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1xE7N1i---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/262/1%2AQbpBVsvJIlaW0pAxRozCrA.gif)

**代码概述**

HTML 包括一个简单的按钮和一个 span 元素。图标由 span 和 span 前后的两个伪元素组成，这两个伪元素看起来像一个汉堡包图标。下面是相关的 HTML 代码:

```
<button class="hamburger\_\_button">
  Menu <span class="hamburger\_\_icon"></span>
</div> 
```

现在是 CSS。下面是渲染汉堡图标的默认样式(只是相关的规则):

```
/\* give the span element and related pseudo-elements the appearance of white lines \*/

.hamburger\_\_icon,
.hamburger\_\_icon::before,
.hamburger\_\_icon::after {
  position: absolute;
  width: 44px; 
  height: 4px;
  border-radius: 4px;
  background-color: #fff;
}

/\* set the span element in the middle of its containing div \*/
.hamburger\_\_icon {
  top: calc(50% - 2px);
  left: calc(50% - 22px);
}

/\* set the content property and left position of the two pseudo-elements\*/
.hamburger\_\_icon::before,
.hamburger\_\_icon::after {
  content: "";
  left: 0;
}

/\* set the bottom property of the before pseudo-element \*/
.hamburger\_\_icon::before {
  bottom: 12px;
}

/\* set the top property of the after pseudo-element \*/
.hamburger\_\_icon::after {
  top: 12px;
} 
```

上面的代码片段创建了三行典型的汉堡包图标外观。

接下来，是悬停风格的时候了。当用户悬停在按钮上时，span 和伪元素将被旋转(使用 [CSS transform rotate()函数](https://developer.mozilla.org/en-US/docs/Web/CSS/rotate))，它们的背景颜色、位置、高度和宽度将改变为典型的关闭图标的形状。代码如下:

```
/\* HOVER STYLES ON THE BUTTON \*/

/\* increase width and height of span element, \*/
/\* recalculate top and left position, rotate it and change background-color \*/
.hamburger\_\_button:hover .hamburger\_\_icon {
  height: 10px;
  width: 110px;
  left: 5px;
  top: calc(50% - 4px);
  transform: rotate(-45deg);
  background-color: #e20650;
}

/\* adjust properties on the after pseudo element \*/
.hamburger\_\_button:hover .hamburger\_\_icon::after {
  width: 110px;
  height: 10px;
  top: -1px;
  transform: rotate(-270deg);
  background-color: #e20650;
}

/\* hide the before pseudo-element by scaling it to 0 \*/
.hamburger\_\_button:hover .hamburger\_\_icon::before {
  transform: scale(0);
} 
```

如果你现在悬停在按钮上，你会立即看到汉堡图标突然变成关闭图标。添加 CSS 过渡将实现我们所追求的渐变效果。

**CSS 转换代码**

如果您将转换添加到悬停状态，这些转换将在用户悬停在按钮上时应用，但不会在鼠标离开按钮时应用。换句话说，汉堡包图标将逐渐平滑地变形为关闭图标，但关闭图标将突然恢复为汉堡包图标。为了确保**动画效果在鼠标进入和鼠标离开**时同样发生，您需要将过渡代码添加到全局默认样式，而不是悬停样式。下面是方法:

```
/\* this is one of the lines of the close icon \*/
.hamburger\_\_icon {
  /\* preceding code stays the same \*/
  transition: all 0.3s linear;
}

/\* this is the other line of the close icon \*/
  .hamburger\_\_icon::after {
  /\* preceding code stays the same \*/
  transition: all 0.3s linear;
} 
```

在一行代码中，您已经告诉浏览器在 0.3 秒的时间内将过渡应用到元素上的所有可动画化的属性，而变化率没有任何变化(使用线性计时函数)。

您可以列出每个 CSS 属性名称，而不是使用关键字 all。例如:

```
.hamburger\_\_button:hover .hamburger\_\_icon {
  /\* preceding code stays the same \*/

  transition: height 0.3s linear,
              width 0.3s linear,
              left 0.3s linear,
              top 0.3s linear,
              transform 0.3s linear,
              background-color 0.3s linear;
} 
```

当您计划将一个过渡应用到选择器上的几个属性时，或者如果您想要**指定任何属性**的变化，如不同的持续时间或计时功能，拼出每个单独的属性是有用的**。事实上，使用这种技术可以提高代码效率和性能。然而，如果您仍然需要动画化相当多的属性，这样做可能会冗长、重复且容易出错。**

测试你的代码:按钮悬停时的变形效果现在应该看起来平滑且交互愉快。此外，尝试不同的过渡计时功能，尝试不同的持续时间值，或者为什么不增加几毫秒的延迟，看看会发生什么。

下面是完整的运行代码:

[https://medium . com/media/d 47279 f 7632 c 07 ADC 815312606079556/href](https://medium.com/media/d47279f7632c07adc815312606079556/href)

### CSS 转场和 JavaScript

当转换元素时，您不局限于悬停事件。您可以将过渡绑定到一个 CSS 类，然后利用 JavaScript 将该 CSS 类添加到您想要制作动画的元素中。

换句话说，CSS 将处理外观，即动画效果，而 JavaScript 通过动态添加和删除 CSS 类来处理行为或 DOM 操作。

为了说明这一点，这里有与上面相同的变形效果，但是是在单击按钮时执行的。

第一步是用您选择的类名替换您之前编写的悬停样式的所有实例。我叫我的班级**切换**。以下是相关片段:

```
.toggled.hamburger\_\_button .hamburger\_\_icon {
  height: 10px;
  width: 110px;
  left: 5px;
  top: calc(50% - 4px);
  transform: rotate(-45deg);
  background-color: #e20650;
}

.toggled.hamburger\_\_button .hamburger\_\_icon::after {
  width: 110px;
  height: 10px;
  top: -1px;
  transform: rotate(-270deg);
  background-color: #e20650;
}

.toggled.hamburger\_\_button .hamburger\_\_icon::before {
  transform: scale(0);
} 
```

使用上面的代码，每次切换的类被添加到按钮时，图标都会变成一个关闭图标。同理，当切换的类被移除时，图标会变回汉堡包图标。

JavaScript 代码的单一任务是切换。按钮点击时切换类别:

```
hamburgerButton.addEventListener( "click", () => hamburgerButton.classList.toggle("toggled") ); 
```

这就是你所需要的。测试您的代码，变形效果应该与您之前创建的悬停示例完全一样:

[https://medium . com/media/58c 13530d 693 f 56345d 328657331 b02e/href](https://medium.com/media/58c13530d693f56345d328657331b02e/href)

**将 CSS 过渡应用到动态创建的 HTML 元素**

上面的例子开箱即用，因为转换应用于静态 DOM 元素，即在 HTML 源代码中硬编码的元素。然而，如果您动态地创建您的元素并使用 JavaScript 将它附加到 DOM，那么应用一个应该触发转换的类将不会达到预期的结果。一旦浏览器呈现元素，过渡效果就结束了，动画也丢失了。

我的意思是。比方说，当你点击页面上的一个按钮时，一个框从顶部滑入。滑入效果是通过上面使用的相同技术实现的:当 transition 属性应用于 box 元素时，动态添加一个类来触发 CSS 转换:

```
.box {
  /\* more code above, transition below \*/
  transition: 1s;
} 
```

这一次的不同之处在于，HTML 文档中没有已经存在的框，而是用 JavaScript 动态创建的。

JavaScript 代码看起来像这样:

```
// add event listener to the button element
button.addEventListener('click', () => {
  // create the box element with a class of .box
  const box = document.createElement("div");
  box.classList.add("box");

  // append the new element box to the DOM
  document.querySelector("body").appendChild(box);

  // add the class that triggers the transition to the box element
  document.querySelector(".box").classList.add("slide-in");
} 
```

看看 CodePen 上的工作演示:

[https://medium . com/media/FD 1 ade 8224848220 B3 f 48 DD 3302 e 14 ea/href](https://medium.com/media/fd1ade8224848220b3f48dd3302e14ea/href)

当你点击该按钮时，该框将立即物化到页面**上，而没有动画**。

为了解决这个问题，您需要在新元素创建一小段时间后添加转换触发类。为此，您可以使用[。setTimeout()方法](https://www.w3schools.com/Jsref/met_win_settimeout.asp)，或者更好的是[。requestAnimationFrame()](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 方法。自从。requestAnimationFrame()是专门为网络动画设计的，这是我用来修复这个演示的方法。

作为第一步，您需要编写一个函数，将转换触发类添加到您的元素中，并调用。requestAnimationFrame():

```
const doTransition = () => {
  // add the class that triggers the transition to the box element
  document.querySelector('.box').classList.add("slide-in");

  // call requestAnimationFrame passing this same function as argument
  window.requestAnimationFrame(doTransition);
}; 
```

下一步是打电话。按钮单击处理程序中的 requestAnimationFrame()，再次传入 doTransition()函数作为参数。

在将盒子附加到 DOM 的代码之后添加这个位:

```
// call requestAnimationFrame passing the doTransition function as argument
window.requestAnimationFrame(doTransition); 
```

你完了！看一下工作演示，了解不同之处:

[https://medium . com/media/9 ea 26 c 53714 f 23 DBF 9 e 549792 DC 7 b 9 c 0/href](https://medium.com/media/9ea26c53714f23dbf9e549792dc7b9c0/href)

### 酷资源

为了更深入地研究 CSS 转换，我列出了一些有用的文章，您可以查看一下:

*   [W3C 规范](https://www.w3.org/TR/css-transitions-1/)
*   [MDN Web 文档:过渡-计时-功能](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-timing-function)
*   [MDN Web 文档:使用 CSS 转换](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions)
*   [MDN Web 文档:可制作动画的 CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)
*   [用 JavaScript 控制 CSS 动画和过渡](https://css-tricks.com/controlling-css-animations-transitions-javascript/)
*   [在自动尺寸上使用 CSS 过渡](https://css-tricks.com/using-css-transitions-auto-dimensions/)

### 结论

在这篇 CSS 过渡的介绍中，我讨论了什么时候在 CSS 关键帧动画上使用过渡是合适的，以及如何使用 JavaScript 在鼠标悬停和点击时使用过渡来变形切换按钮上的汉堡图标。

如果你对 CSS 转换有什么有趣的用法想分享，我很乐意听听你的意见。

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *