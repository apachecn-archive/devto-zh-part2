# 如何使用 CSS 过渡属性

> 原文：<https://dev.to/brittanmcg/how-to-use-the-css-transition-property-20mm>

Sabri Tuzcu 在 Unsplash 上拍摄的照片

# 如何使用 CSS `transition`属性

### 或者我如何理解 CSS 转场

我正在学习 CSS 动画，我决定从 CSS `transition`属性开始。

在通读之前，你应该对 HTML 和 CSS 有一个基本的了解。

这将尽可能深入地介绍 CSS 转换属性。这里还会有其他的概念，但我会暂时忽略它们。这应该是相对基本和容易理解的，因为我发现这个特殊的属性非常容易理解。

有 4 个`transition`动画属性。它们是:

*   过渡属性
*   过渡持续时间
*   过渡时间函数
*   过渡延迟

我将在这里逐一分析这些属性。

`transition-property`:这是你想要制作动画的属性。这可能是`opacity`、`width`、`background`或任何数量的不同属性。这里要理解的关键是你正在定义什么将被动画化。需要记住的是，并不是所有的属性都是可动画的(不确定这是不是一个词)。一个很好的经验法则是，如果它有一个中间状态，那么你可能可以激活它。

|  | 起始状态 | 过渡 | 结束状态 |
| --- | --- | --- | --- |
| `opacity` | Zero | Zero point five | one |
| `display` | 没有人 | 没有中间状态😢 | 街区 |

这是动画将持续的时间。或者需要多长时间才能完成。当你使用这个持续时间的时候要记住的是，如果这个持续时间太长，会让用户很烦。或者，它的不和谐，如果它发生得太快，或者可能察觉不到它甚至是动画。试着找到一个快乐的、感觉正确的、有目的的持续时间的媒介。

这是动画开始前的延迟时间。你可能想让多个动画同时发生，但是你可能想在开始转换变化的颜色之前延迟背景转换`0.25s`。

这个有点难以理解。本质上，它是你给你的动画在这个动画中执行的功能。

您可以选择:

*   `ease` -默认值(可以说是最常用的)。进行软启动并进入过渡状态。
*   开始时缓慢，结束时快速/突然
*   `ease-out` -开始时快速/突然，结束时缓慢
*   老实说，我真的看不出`ease-in`函数有什么不同，但它基本上是以上两者的混合。
*   `linear` -全程速度一致
*   `cubic-bezier` -这是传递给一个函数的一系列 xy 坐标，该函数将映射定时曲线的控制点。这个问题有点棘手，值得进行比我在这里提供的更深入的描述。[三次贝塞尔](https://callmenick.com/dev/level-up-animations-cubic-bezier/)

您可以通过使用`transition`属性并添加如下所示的属性来简化这些属性。
T1】

下面是一个按钮元素的过渡示例。

我们将从一些 HTML 和 CSS 开始

```
<!--Start HTML-->
<body>
  <main class="container">
    <button class="btn">Submit</button>
  </main>
</body> 
```

```
/* Start CSS */
body {
  background: lightblue;
}

.container {
  text-align: center;
}

.btn {
  margin: 20px auto;
  padding: 20px;
  width: 180px;
  border-radius: 20px;
  font-size: 1.4em;
} 
```

然后我们将添加颜色和悬停状态

```
/* ... */

.btn {
  margin: 20px auto;
  padding: 20px;
  width: 180px;
  border-radius: 20px;
  font-size: 1.4em;
  background: papayawhip;
}

.btn:hover {
  background: azure;
} 
```

然后我们将添加一个过渡

```
/* ... */

.btn {
  margin: 20px auto;
  padding: 20px;
  width: 180px;
  border-radius: 20px;
  font-size: 1.4em;
  background: papayawhip;
  transition: background .5s ease; 
  /* Targets the background property and sets it to ease into the azure color in .5 seconds */
}

.btn:hover {
  background: azure;
} 
```

我们也可以添加一个成长动画

```
/* ... */

.btn {
  /* ... */
  transition: background .5s ease .25s, width .25s ease-in-out;
}

.btn:hover {
  background: azure;
  width: 210px;
} 
```

您可以在这里看到工作示例:
[https://codepen.io/konamax123/embed/jzdXve/?height=600&default-tab=result&embed-version=2](https://codepen.io/konamax123/embed/jzdXve/?height=600&default-tab=result&embed-version=2)
这是一个相当做作的示例，但我觉得它说明了该属性的一个基本用例，而没有增加太多的混乱。

**下一步**
接下来我将讨论 CSS 转换。我很高兴看到我如何将这两种特性结合起来，做出很酷的东西。

### 侧注

这篇博文是我试图遵循的学习框架的一部分。在学习过程的每一步，您都必须完成几个模块。教学是其中的一个模块，这也是这篇博文的内容。这个框架来自 John Sonmez 的快速学习 10 步。约翰的课程。我不赚任何钱，如果你买这个，因为我不属于任何方式。不过，我强烈推荐它。