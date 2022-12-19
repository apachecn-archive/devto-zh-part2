# 使用 CSS Flexbox 伸缩

> 原文：<https://dev.to/c0depanda/flexing-with-css-flexbox-1a2l>

[![](../Images/fc63b1a2371911f47e34163b7da40471.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I0s7oD8H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AYbtE_wqudjWLSmNbFLL1uA.png)

Flexbox 也称为 flexible box，是 CSS 中的一种布局模型，使设计响应性布局变得非常容易。

Flexbox 布局模型背后的整个想法是允许元素在任何方向上布局，调整它们的大小以填充未使用的空间，或者收缩以避免溢出它们的父元素，无论是水平还是垂直。

为了真正能够使用 Flexbox 进行伸缩，我们必须了解它是如何工作的。

让我们把它分解成两个性质，即:

1.  柔性容器
2.  弹性项目

### 伸缩容器

这是父 html 元素，包含您想要布局的项目。

要使用任何 flex 属性，必须创建该容器。

它创建了允许所有其他 flex 属性工作的上下文。

[https://medium . com/media/edd 01 adbf 037 f 51477 ed 4 e 68 a3 F4 FD 26/href](https://medium.com/media/edd01adbf037f51477ed4e68a3f4fd26/href)

#### 对齐-项目

这允许您垂直对齐 flex 容器中的项目，而不管项目相对于其 flex 容器或彼此的高度。

它接受的值有:*|**|*伸缩结束*** | ***中心*|**|**|*基线*|**|**|*拉伸****

 *[https://medium . com/media/b 4398 c7e 3515668 c 3424631285 fc 8 bb 1/href](https://medium.com/media/b4398c7e3515668c3424631285fc8bb1/href)

#### 自圆其说-内容

这与 **align-items 相反，**它水平对齐其项目，而不考虑项目相对于其容器或彼此的宽度。

取值:***flex-start***|**|*flex-end*|**|**|*center*|**|**|*space-between*|**|*space-around*|**|**|*space-equally*****

[https://medium . com/media/3aa 0103329264485 e83d 595 e 36191706/href](https://medium.com/media/3aa0103329264485e83d595e36191706/href)

#### 柔性包装

flex-wrap 属性指定 flex 项目是否应该换行。

默认情况下，所有的 flex 项都试图放在一行中，但是这个属性告诉浏览器当它们变得太多而不能放在一行中时，将它们分成另一行。

我们所说的这条线也被称为 F **lex 线**。

值:***nowrap***|**|*wrap***|**|*wrap-reverse***

[https://medium . com/media/44 ed8 e 66 e 4579412 b 07 cffef 96 DC 13 c 3/href](https://medium.com/media/44ed8e66e4579412b07cffef96dc13c3/href)

#### 对齐-内容

该属性修改了**柔性包装**属性的行为。

它的行为本质上类似于 **align-items** 属性，只是它对齐的是伸缩线而不是伸缩项。

要使此属性起作用，必须在 flex 容器上设置 flex-wrap: wrap，并且 flex 行必须不止一条。

取值:***flex-start***|**|*flex-end*|**|**|*center*|**|**|*space-between*|**|*space-around*|**|**|*stretch*****

[https://medium . com/media/4e 4275 b 97d 9 bee 94038471 c0d 269 a93b/href](https://medium.com/media/4e4275b97d9bee94038471c0d269a93b/href)

#### 伸缩方向

这定义了浏览器应该在哪个方向堆叠伸缩项，即垂直还是水平。

取值: ***行***|**|*行-反转***|**|*列*|**|*列-反转*****

[https://medium . com/media/5c 637 af 5 f 891 b 346 BF 645 aa1a 76170 a 0/href](https://medium.com/media/5c637af5f891b346bf645aa1a76170a0/href)

#### 伸缩流

这是 flex-direction 和 flex-wrap 的简写，所以不用写下面的；

```
.container {
   display: flex;
   flex-direction: column;
   flex-wrap: wrap;
} 
```

你可以这样做。

```
.container {
   display: flex;
   flex-flow: column wrap;
} 
```

[![](../Images/2063f22e152ef36aa267c13ae9842601.png)T2】](https://logrocket.com/?cid=banner_a)

### 弹性项目

这是 Flex 容器的直接子容器，就像 Flex 容器一样，它们也有可以应用的属性。

#### 下达命令

此属性允许您更改弹性项目的排列顺序。即根据 HTML 结构源代码。

同一容器中的 Flex 项目根据 **order** 值以升序排列，而具有相同 order 值的 flex 项目按照它们在源代码中出现的顺序排列。

数值: ***(正或负)***

[https://medium . com/media/b 89ad 7690 AC 49 a 38616 ea 1767 c6d 7474/href](https://medium.com/media/b89ad7690ac49a38616ea1767c6d7474/href)

#### 自我对齐

顾名思义，属性允许您在 flex 容器内垂直对齐选定的 flex 项。

它的工作方式与 align-items 基本相同，只是它是针对单个 Flex 项目的。

当您只想对齐单个 Flex 项目而不是 Flex 容器中的所有 Flex 项目时，这很有用。

**注意:**这个属性覆盖了 flex 容器的 align-items 属性。

值: ***自动|伸缩-开始|伸缩-结束|中心|基线|拉伸***

[https://medium . com/media/7ba 27 aa 32 e 98 c 1383 BF 3 ba 7 DD 4625118/href](https://medium.com/media/7ba27aa32e98c1383bf3ba7dd4625118/href)

#### 弹性基础

此属性设置选定伸缩项的初始大小。这与宽度属性的工作方式非常相似。

flex-basis 属性接受以[长度单位](https://www.w3schools.com/cssref/css_units.asp)表示的值，即 px、em、rem、百分比等。它还接受 auto，这基本上是基于是否设置了 flex-grow 或者基于 flex 项的内容大小来决定如何分布 flex 项。

[https://medium . com/media/eb5b 289709639 a 6 afdd 4 CCC 7 DC 62 C9 bb/href](https://medium.com/media/eb5b289709639a6afdd4ccc7dc62c9bb/href)

#### 伸缩增长

这个属性赋予 flex 项目神奇的力量，使它们可以增长到填满一个 flex 容器，或者比容器中的其他元素占用更多的空间。

它基本上指定了项目应该在 flex 容器中占据多少空间。

值: ***(正)***

[https://medium . com/media/a 495 a9 a 8720 bdbaf 603 DC 52 cbfd 832 aa/href](https://medium.com/media/a495a9a8720bdbaf603dc52cbfd832aa/href)

#### 伸缩

这与 flex-grow 相反，flex-grow 基本上允许 flex 项目在必要时收缩。即 flex 容器中的其他项目试图占用更多空间。

值: ***(正)***

[https://medium . com/media/3b 64 e 29 FB 7 DC 700 a 9672 b5 DD 6b 785910/href](https://medium.com/media/3b64e29fb7dc700a9672b5dd6b785910/href)

根据上面的示例，有 5 个基于 flex 的 Flex 项目:它们设置了 200px，总共是 1000px。

另一方面，flex 容器被设置为 400 像素，这比 flex 项目小，但是因为 flex-wrap 默认为 nowrap，所以我们会让所有项目尝试适应 Flex 容器，从而均匀地分布它们自己。

一旦在这些项目上设置了伸缩值，它们将开始根据这些值进行调整。

与其他弹性项目相比，弹性收缩值越高，收缩越多。

#### 伸缩

这是 flex-grow、flex-shrink 和 flex-basis 的简写属性，但是 flex-shrink 和 flex-basis 是可选的，也就是说，您可以只设置 flex-grow，其余的将设置为默认值。

默认情况下，这些值设置为 flex: 0 1 auto，即 flex-grow: 0、flex-shrink: 1 和 flex-basis: auto。

觉得这篇文章有帮助？别忘了鼓掌分享:)

### Plug: [LogRocket](http://logrocket.com) ，一款适用于网络应用的 DVR

[![](../Images/d56be9e9e36d8fa98c6959f7097b7787.png)T2】](http://logrocket.com)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * **